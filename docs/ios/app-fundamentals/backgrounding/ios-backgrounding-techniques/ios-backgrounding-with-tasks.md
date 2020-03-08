---
title: iOS 后台处理及任务
description: 本文档介绍如何使用后台任务在应用程序置于后台后执行长时间运行的任务。
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f39ab83e00db1abd6508d26a9280fb708e681445
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915758"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS 后台处理及任务

在 iOS 上执行后台处理的最简单方法是将后台处理要求分解到任务中，并在后台运行任务。 任务处于严格的时间限制下，在应用程序移动到 iOS 6 上的后台并且在 iOS 7 + 上小于10分钟后，处理时间通常大约为600秒（10分钟）。

可将后台任务分为三个类别：

1. **后台安全任务**-在应用程序中，在应用程序中有一项不希望中断的任务应该进入后台。
1. **DidEnterBackground 任务**-在 `DidEnterBackground` 应用程序生命周期方法中调用，以帮助进行清理和省电。
1. **后台传输（ios 7 +）** -一种特殊类型的后台任务，用于在 iOS 7 上执行网络传输。 与常规任务不同，后台传输没有预先确定的时间限制。

可以安全地在 iOS 6 和 iOS 7 上使用后台安全和 `DidEnterBackground` 任务，但有一些细微的差异。 让我们更详细地调查这两种类型的任务。

## <a name="creating-background-safe-tasks"></a>创建后台安全任务

某些应用程序包含不应由 iOS 中断的任务应应用程序更改状态。 防止这些任务被中断的一种方法是将其作为长时间运行的任务注册到 iOS。 如果用户将应用放入后台，则可以在应用程序中的任何位置使用此模式，在这种情况下，你不希望任务被中断。 此模式的理想形式是将新用户的注册信息发送到你的服务器，或者验证登录信息等任务。

下面的代码段演示如何将任务注册为在后台运行：

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

注册过程将具有唯一标识符的任务配对，`taskID`，然后将其包装在匹配 `BeginBackgroundTask` 和 `EndBackgroundTask` 调用中。 为生成标识符，我们对 `UIApplication` 对象调用 `BeginBackgroundTask` 方法，然后启动长时间运行的任务（通常在新线程上）。 任务完成后，调用 `EndBackgroundTask` 并传入同一标识符。 这一点很重要，因为如果 `BeginBackgroundTask` 调用没有匹配的 `EndBackgroundTask`，iOS 将终止应用程序。

> [!IMPORTANT]
> 基于应用程序的需求，可在主线程或后台线程上运行后台安全任务。

## <a name="performing-tasks-during-didenterbackground"></a>在 DidEnterBackground 期间执行任务

除了使长时间运行的任务处于后台安全状态外，注册还可用于在后台放置应用程序时启动任务。 iOS 在*AppDelegate*类中提供了一个名为 `DidEnterBackground` 的事件方法，该方法可用于保存应用程序状态、保存用户数据以及在应用程序进入后台之前加密敏感内容。 应用程序从该方法返回大约5秒，否则将终止。 因此，可能需要5秒以上才能完成的清理任务可从 `DidEnterBackground` 方法内部调用。 这些任务必须在单独的线程上调用。

此过程与注册长时间运行的任务几乎完全相同。 下面的代码段演示了此操作：

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

首先，我们将覆盖 `AppDelegate`中的 `DidEnterBackground` 方法，我们将通过 `BeginBackgroundTask` 来注册任务，就像在前面的示例中所做的那样。 接下来，我们将生成一个新线程并执行长时间运行的任务。 请注意，`EndBackgroundTask` 调用现在是在长时间运行的任务内进行的，因为 `DidEnterBackground` 方法已返回。

> [!IMPORTANT]
> iOS 使用[监视程序机制](https://developer.apple.com/library/ios/qa/qa1693/_index.html)来确保应用程序的 UI 保持响应。 在 `DidEnterBackground` 中花费太多时间的应用程序在 UI 中将变得不响应。 开始例行关闭要在后台运行的任务，`DidEnterBackground` 可以及时返回，使 UI 保持响应状态，阻止监视器终止应用程序。

## <a name="handling-background-task-time-limits"></a>处理后台任务时间限制

iOS 会严格限制后台任务的运行时间，如果未在分配的时间内进行 `EndBackgroundTask` 调用，则应用程序将终止。 通过跟踪剩余的后台处理时间，并在必要时使用过期处理程序，我们可以避免 iOS 终止应用程序。

### <a name="accessing-background-time-remaining"></a>访问剩余背景时间

如果将具有已注册任务的应用程序移动到后台，则已注册任务将大约600秒运行。 使用 `UIApplication` 类的静态 `BackgroundTimeRemaining` 属性，可以检查该任务必须完成的时间。 下面的代码将为我们提供后台任务剩余时间（秒）：

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>利用过期处理程序避免应用程序终止

除了提供对 `BackgroundTimeRemaining` 属性的访问权限外，iOS 还提供了一种通过**过期处理程序**来处理后台时间过期的方式。 这是可选的代码块，当分配给任务的时间即将过期时，将执行该代码块。 过期处理程序中的代码调用 `EndBackgroundTask` 并传入任务 ID，这表示应用程序正常工作，并阻止 iOS 终止应用程序，即使该任务超时。 在过期处理程序中，以及在正常执行过程中，必须调用 `EndBackgroundTask`。 

过期处理程序使用 lambda 表达式表示为匿名函数，如下所示：

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.BackgroundTimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

尽管运行代码不需要过期处理程序，但应始终将过期处理程序与后台任务一起使用。

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>IOS 7 + 中的后台任务

与后台任务有关的 iOS 7 中的最大更改不是任务的实现方式，而是在运行时执行。

请记住，在后台运行的任务需要600秒钟才能完成。 此限制的一个原因是，在后台运行的任务会使设备在任务持续期间处于唤醒状态：

 [![](ios-backgrounding-with-tasks-images/ios6.png "Graph of the task keeping the app awake pre-iOS 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

iOS 7 后台处理已针对更长的电池寿命进行了优化。 在 iOS 7 中，后台处理成为了机会：而不是让设备处于唤醒状态，而是在设备进入睡眠状态时执行任务，而是在设备唤醒来处理电话呼叫、通知、传入电子邮件和其他常见中断。 下图提供了对任务的分解方式的深入了解：

 [![](ios-backgrounding-with-tasks-images/ios7.png "Graph of the task being broken into chunks post-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

因为任务运行时间不是连续的，所以在 iOS 7 中，执行网络传输的任务必须以不同的方式进行处理。 鼓励开发人员使用 `NSURlSession` API 来处理网络传输。 下一部分概述了后台传输。

 <a name="background-transfers" />

## <a name="background-transfers"></a>后台传输

IOS 7 中的后台传输主干是新的 `NSURLSession` API。 `NSURLSession` 允许我们创建任务：

1. 通过网络和设备中断传输内容。
1. 上传和下载大型文件（*后台传输服务*）。

让我们详细了解一下它的工作原理。

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` 是一种功能强大的 API，用于通过网络传输内容。 它提供了一组工具，用于通过网络中断和应用程序状态中的更改来处理数据传输。

`NSURLSession` API 创建一个或多个会话，进而通过网络将任务集中到相关数据块。 任务以异步方式运行以快速可靠地传输数据。 由于 `NSURLSession` 是异步的，因此每个会话都需要完成处理程序块，以让系统和应用程序知道传输何时完成。

若要执行在 iOS 7 和 iOS 之前7上都有效的网络传输，请检查 `NSURLSession` 是否可用于排队传输，如果不是，则使用常规后台任务执行传输：

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> 避免从兼容 iOS 6 的代码中的后台更新 UI，因为 iOS 6 不支持后台 UI 更新，并将终止应用程序。

`NSURLSession` API 包括一组丰富的功能，可用于处理身份验证、管理失败的传输和报告客户端-而不是服务器端错误。 它有助于桥接 iOS 7 中引入的任务运行时间中断，并为快速可靠地传输大文件提供支持。 下一节将探讨此第二项功能。

### <a name="background-transfer-service"></a>后台传输服务

在 iOS 7 之前，在后台上传或下载文件不可靠。 后台任务会在有限的时间运行，但传输文件所花费的时间因网络和文件大小而异。 在 iOS 7 中，可以使用 `NSURLSession` 成功上传和下载大文件。 在后台处理大型文件的网络传输的特定 `NSURLSession` 会话类型称为*后台传输服务*。

使用后台传输服务启动的传输由操作系统管理，并提供 Api 来处理身份验证和错误。 由于传输不受任意时间限制的限制，因此它们可用于上传或下载大型文件、在后台自动更新内容等。 有关如何实现服务的详细信息，请参阅[后台传输演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)。

后台传输服务通常与后台提取或远程通知配对，以帮助应用程序在后台刷新内容。 在接下来的两个部分中，我们将介绍如何在 iOS 6 和 iOS 7 上将整个应用程序注册到后台运行。
