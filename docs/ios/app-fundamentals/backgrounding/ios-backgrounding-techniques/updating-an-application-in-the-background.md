---
title: 在后台更新 Xamarin iOS 应用
description: 本文档介绍了更新后台的 Xamarin iOS 应用的各种方法，例如区域监视、后台提取和远程通知。
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2d56af364d63ff78bafbdd7d8043ae4d75d97959
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915756"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>在后台更新 Xamarin iOS 应用

后台刷新是指唤醒处于挂起或未运行状态的应用程序，并使用新内容更新该应用程序的过程。 iOS 提供三个用于在后台刷新内容的选项：

1. *区域监视*和*重要位置更改了服务*位置感知 api 基于用户位置的更改触发后台更新。 这些 Api 可用于在不基于位置的 iOS 6 应用程序中刷新内容，而其他选项则不可用。
1. *后台提取（iOS 7 +）* -一种临时方法，用于刷新*经常*更新的*非关键*内容。
1. *远程通知（iOS 7 +）* -接收推送通知的应用程序可以使用通知来触发后台内容刷新。 此方法可用于更新*偶尔*更新的*重要、区分时间*的内容。

以下各节介绍了这些选项的基本知识。

## <a name="region-monitoring-and-significant-location-changes"></a>区域监视和重要位置更改

iOS 提供两个识别位置的 Api，具有后台处理功能：

1. *区域监视*是指设置具有边界的区域的过程，并在用户进入或退出某个区域时唤醒设备。 区域是循环的，大小可以不同。 当用户跨越区域边界时，设备将唤醒以处理事件，通常是通过触发通知或开始例行停止任务。 区域监视要求 GPS，并增加电池和数据的使用。
1. *重要的位置更改服务*是一种更简单的节能选项，可用于具有手机网络收发器的设备。 当设备切换单元塔时，侦听重要位置更改的应用程序将收到通知。 此服务可用于唤醒挂起或终止的应用程序，并提供在后台检查新内容的机会。 除非与[后台任务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md)配对，否则后台活动限制为大约10秒。

应用程序不需要 `UIBackgroundMode` 位置即可使用这些位置感知 Api。 由于 iOS 并不跟踪当设备唤醒用户所在位置的更改时可以运行的任务类型，因此这些 Api 提供了一个解决方法，用于在 iOS 6 上的后台更新内容。 请*记住，使用基于位置的 api 触发后台更新会在设备资源上进行绘制，并且可能会使不了解应用程序需要访问其位置的用户感到困惑*。 在尚未使用位置 Api 的应用程序中实现区域监视或对后台处理的重要位置更改时，请使用判断。

使用位置监视进行后台处理的应用在 iOS 6 中公开了一个缺陷：如果应用程序的需求不适合后台必需的类别，则该应用程序的后台处理选项有限。 由于引入了两个新的 Api、*后台提取*和*远程通知*，iOS 7 （及更高版本）为更多应用程序提供了后台处理的机会。 接下来的两部分介绍了这些新 Api。

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>后台提取（iOS 7 及更高版本）

在 iOS 6 中，应用程序会输入前台加载新内容所需的时间，并向用户简要介绍已查看的内容。 后台提取允许应用程序在用户启动应用程序*之前*加载新数据，并为用户提供最新的内容。

若要实现后台提取，请编辑*info.plist* ，并选中 "**启用后台模式**" 和 "**后台提取**" 复选框：

 [![](updating-an-application-in-the-background-images/fetch.png "Edit the Info.plist and check the Enable Background Modes and Background Fetch check boxes")](updating-an-application-in-the-background-images/fetch.png#lightbox)

接下来，在 `AppDelegate`中，重写 `FinishedLaunching` 方法以设置最小提取间隔。 在此示例中，我们让 OS 决定获取新内容的频率：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

最后，通过重写 `AppDelegate`中的 `PerformFetch` 方法并传入*完成处理程序*来执行提取。 完成处理程序是一个委托，它采用 `UIBackgroundFetchResult`：

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

完成更新内容后，我们会通过调用具有适当状态的完成处理程序来获知操作系统。 iOS 提供三个用于完成处理程序状态的选项：

1. `UIBackgroundFetchResult.NewData`-获取新内容并更新应用程序时调用。
1. `UIBackgroundFetchResult.NoData`-在提取新内容时调用，但没有可用的内容。
1. `UIBackgroundFetchResult.Failed`-适用于错误处理，当提取无法完成时，将调用此方法。

使用后台获取的应用程序可以调用从后台更新 UI。 当用户打开应用时，UI 将是最新的并显示新内容。 此操作还会更新应用程序的应用切换器快照，以便用户可以查看应用程序是否具有新内容。

> [!IMPORTANT]
> 调用 `PerformFetch` 后，应用程序将有大约30秒的时间开始下载新内容，并调用完成处理程序块。 如果此操作花费的时间太长，将终止应用程序。 下载媒体或其他大文件时，请考虑将后台提取与_后台传输服务_一起使用。

### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

在上面的示例代码中，我们通过将最小提取间隔设置为 `BackgroundFetchIntervalMinimum`来确定获取新内容的频率。 iOS 为提取间隔提供三个选项：

1. `BackgroundFetchIntervalNever`-通知系统永远不要提取新内容。 使用此功能可在某些情况下（例如，用户未登录时）关闭提取。 这是提取间隔的默认值。 
1. `BackgroundFetchIntervalMinimum`-让系统根据用户模式、电池寿命、数据使用情况以及其他应用程序的需求来决定获取的频率。
1. `BackgroundFetchIntervalCustom`-如果你知道应用程序内容的更新频率，你可以在每次提取之后指定 "睡眠" 间隔，在此期间，应用程序将无法获取新内容。 该间隔过后，系统将确定何时提取内容。

`BackgroundFetchIntervalMinimum` 和 `BackgroundFetchIntervalCustom` 都依赖于系统来计划提取。 此间隔是动态的，适应设备需求以及单个用户的习惯。 例如，如果一个用户每隔早上检查一次应用程序，而另一个用户每隔一小时检查一次，则在每次打开应用程序时，iOS 将确保每个用户的内容都是最新的。

应将后台提取用于经常使用非关键内容进行更新的应用程序。 对于具有关键更新的应用程序，应使用远程通知。 远程通知基于后台提取，并共享相同的完成处理程序。 接下来，我们将深入探讨远程通知。

 <a name="remote_notifications" />

## <a name="remote-notifications-ios-7-and-greater"></a>远程通知（iOS 7 及更高版本）

推送通知是通过*Apple Push Notification 服务（APNs）* 从提供程序发送到设备的 JSON 消息。

在 iOS 6 中，传入的推送通知告知系统向用户通知应用程序中发生了一些有趣的事情。 单击该通知会将应用程序从 "挂起" 或 "已终止" 状态拉出，应用将开始更新内容。 iOS 7 （及更高版本）通过为应用程序提供一种*在后台更新*内容的机会来扩展普通推送通知，以便用户可以打开应用程序并立即显示新内容。

若要实施远程通知，请编辑*info.plist* ，并选中 "**启用后台模式**" 和 "**远程通知**" 复选框：

 [![](updating-an-application-in-the-background-images/remote.png "Background Mode set to Enable Background Modes and Remote notifications")](updating-an-application-in-the-background-images/remote.png#lightbox)

接下来，将推送通知本身的 `content-available` 标志设置为1。 这使应用程序在显示警报之前知道要提取新内容：

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

在*AppDelegate*中，重写 `DidReceiveRemoteNotification` 方法以检查可用内容的通知有效负载，并调用相应的完成处理程序块：

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

远程通知应用于包含对应用程序功能至关重要的内容的不频繁更新。 有关远程通知的详细信息，请参阅[在 iOS 中的 Xamarin 推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)指南。

> [!IMPORTANT]
> 由于远程通知中的更新机制基于后台提取，因此应用程序必须启动新内容的下载，并在收到通知后的30秒内调用完成处理程序块，否则 iOS 会终止应用程序。 在后台下载媒体或其他大文件时，请考虑将远程通知与_后台传输服务_配对。

### <a name="silent-remote-notifications"></a>无提示远程通知

远程通知是一种简单的方法，通知应用程序的更新并开始获取新内容，但在某些情况下，我们不需要通知用户发生了哪些更改。 例如，如果用户将文件标记为要进行同步，则每次文件更新时都不需要通知它们。 文件同步并不是一种奇怪的事件，也不需要用户立即关注。 用户在打开该文件时，会希望其保持最新状态。

对于上述情况，iOS 允许以无提示方式发送推送通知，即没有警报。 若要将定期通知转换为无提示通知，只需从通知负载中删除警报：

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>速率限制

从开发人员的角度来看，普通和无提示通知之间的最大区别在于，无提示推送是按速率限制的。 如果推送速率太高，则 APNs 将延迟向设备传递无提示推送。 这是为了确保应用程序不会耗尽具有太多缄默通知的设备资源。

但是，APNs 会使无提示的通知与正常的远程通知或保持活动响应一起 "发送"。 因为定期通知不受速率限制，所以可用于将存储的无提示通知从 APNs 推送到设备，如下图所示：

 [![](updating-an-application-in-the-background-images/silent.png "Regular notifications can be used to push stored silent notifications from the APNs to the device, as illustrated by this diagram")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple 鼓励开发人员在应用程序需要时发送无提示推送通知，并让 APNs 计划其交付。

在本部分中，我们介绍了用于在后台刷新内容的各种选项，以运行不适合后台必需类别的任务。 现在，让我们来看看其中的一些 Api 的操作。

 [下一步：第4部分-iOS 后台处理演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
