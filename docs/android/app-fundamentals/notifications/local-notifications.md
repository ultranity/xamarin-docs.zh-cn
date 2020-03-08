---
title: Android 上的本地通知
description: 本部分说明如何在 Xamarin 中实现本地通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 API。
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 617c04e2f40af535fb381362a389524d693fad0b
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78913462"
---
# <a name="local-notifications-on-android"></a>Android 中的本地通知

_本部分说明如何在 Xamarin 中实现本地通知。它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 API。_

## <a name="local-notifications-overview"></a>本地通知概述

Android 提供两个系统控制的区域，用于向用户显示通知图标和通知信息。 首次发布通知时，它的图标将显示在*通知区域*中，如以下屏幕截图所示：

![设备上的示例通知区域](local-notifications-images/01-notification-shade.png)

若要获取有关通知的详细信息，用户可以打开通知抽屉（这会展开每个通知图标以显示通知内容）并执行与通知关联的任何操作。 以下屏幕截图显示了与上面显示的通知区域相对应的*通知抽屉*：

[显示三个通知的 ![示例通知抽屉](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Android 通知使用两种类型的布局：

- ***基本布局***&ndash; 简洁、固定的演示文稿格式。

- ***展开布局***&ndash; 可以扩展到更大大小的演示文稿格式，以显示详细信息。

以下各节介绍了其中的每种布局类型（以及如何创建它们）。

> [!NOTE]
> 本指南重点介绍[Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中的[notificationcompat.builder api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) 。 这些 Api 可确保最大程度地向后兼容 Android 4.0 （API 级别14）。

### <a name="base-layout"></a>基本布局

所有 Android 通知都以基本布局格式生成，其中至少包括以下元素：

1. *通知图标*（表示原始应用程序），如果应用支持不同类型的通知，则为通知类型。

2. 通知*标题*，如果通知是个人消息，则为发送方的名称。

3. 通知消息。

4. *时间戳*。

这些元素显示如下图所示：

[通知元素 ![位置](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

基本布局的高度限制为64与密度无关的像素（dp）。 默认情况下，Android 创建这种基本通知样式。

或者，通知可以显示一个表示应用程序或发件人照片的大图标。 当在 Android 5.0 及更高版本的通知中使用大图标时，小通知图标会显示为大图标上的徽章：

![简单通知照片](local-notifications-images/04-simple-notification-photo.png)

从 Android 5.0 开始，通知也可能出现在锁屏界面上：

[![锁屏界面通知](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

用户可以双击锁定屏幕通知来解锁设备，跳转到源自该通知的应用程序，或轻扫以消除通知。 应用可以设置通知的可见性级别以控制锁定屏幕上显示的内容，用户可以选择是否允许敏感内容显示在锁定屏幕通知中。

Android 5.0 引入了高优先级的通知演示文稿格式，称为 "*打印头*"。 将通知从屏幕顶部向下滑动几秒钟，然后回回通知区域：

[![示例打印头通知](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

"打印头通知" 使系统 UI 可以将重要信息放在用户前面，而不会中断当前正在运行的活动的状态。

Android 包括对通知元数据的支持，以便可以智能地对通知进行排序和显示。 通知元数据还控制通知在锁屏界面上的显示方式。 应用程序可以设置以下通知元数据类型：

- **优先级 &ndash; 优先级**决定如何以及何时提供通知。 例如，在 Android 5.0 中，高优先级通知显示为打印头通知。

- **可见性**&ndash; 指定通知出现在锁屏界面上时要显示的通知内容量。

- **类别**&ndash; 通知系统如何在各种情况下（如设备处于 "*请勿打扰*" 模式时）处理通知。

> [!NOTE]
> **可见性**和**类别**是在 android 5.0 中引入的，在早期版本的 android 中不可用。 从 Android 8.0 开始，[通知通道](#notif-chan)用于控制向用户显示通知的方式。

### <a name="expanded-layouts"></a>扩展的布局

从 Android 4.1 开始，可以对通知进行配置，使用户能够扩展通知的高度以查看更多内容。 例如，下面的示例演示了处于合同模式的展开布局通知：

![合同通知](local-notifications-images/07-contracted-notification.png)

展开此通知后，它将显示整个消息：

![扩展通知](local-notifications-images/08-expanded-notification.png)

Android 为单事件通知支持三个扩展的布局样式：

- "***大文本***" &ndash; 在 "合同" 模式下，显示消息第一行的摘录，后跟两个句点。 在展开模式下，显示整个消息（如上面的示例所示）。

- ***收件箱***&ndash; 在合同模式下显示新消息的数量。 在展开模式下，显示第一封电子邮件或收件箱中的邮件列表。

- 在合同模式下 &ndash;***图像***，只显示消息正文。 在展开模式下，显示文本和图像。

[除基本通知外](#beyond-the-basic-notification)（本文后面）介绍如何创建*大文本*、*收件箱*和*图像*通知。

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>通知通道

从 Android 8.0 （Oreo）开始，可以使用 "*通知通道*" 功能为要显示的每种类型的通知创建用户自定义通道。 通知通道使你可以对通知进行分组，以便发布到通道的所有通知都具有相同的行为。 例如，你可能有一个通知通道，该通道用于需要立即关注的通知，还提供了一个单独的 "更安静" 通道用于信息性消息。

随 Android Oreo 一起安装的**YouTube**应用列出了两种通知类别：**下载通知**和**一般通知**：

[Android Oreo 中的 YouTube ![通知屏幕](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

其中每个类别对应于一个通知通道。 YouTube 应用实现了**下载通知**通道和**一般通知**通道。 用户可以点击 "**下载通知**"，其中显示了应用下载通知通道的 "设置" 屏幕：

[![下载适用于 YouTube 应用的通知屏幕](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

在此屏幕中，用户可以通过执行以下操作来修改**下载**通知通道的行为：

- 将 "重要性" 级别设置为 "**紧急**"、"**高**"、"**中等**" 或 "**低**"，这将配置声音级别和视觉中断。

- 打开或关闭通知点。

- 打开或关闭闪烁的灯。

- 在锁定屏幕上显示或隐藏通知。

- 替代 "**请勿打扰**" 设置。

**一般通知**通道具有类似的设置：

[![YouTube 应用的常规通知屏幕](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

请注意，不能完全控制通知通道与用户交互的方式，&ndash; 用户可以修改设备上任何通知通道的设置，如上面的屏幕截图中所示。 不过，您可以配置默认值（如下所述）。 如这些示例所示，新的 "通知通道" 功能使用户能够更细致地控制不同种类的通知。

## <a name="notification-creation"></a>通知创建

若要在 Android 中创建通知，请使用[Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet 包中的[notificationcompat.builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder)类。 此类使你可以在较旧版本的 Android 上创建和发布通知。
还将讨论 `NotificationCompat.Builder`。

`NotificationCompat.Builder` 提供用于设置通知中各种选项的方法，例如：

- 内容，包括标题、消息文本和通知图标。

- 通知的样式，如 "*大文本*"、 *"收件箱*" 或 "*图像*样式"。

- 通知的优先级：最小、最小、默认值、最大值或最大值。 在 Android 8.0 及更高版本中，通过[_通知通道_](#notification-channels)设置优先级。

- 锁定屏幕上通知的可见性：公共、私有或机密。

- 有助于 Android 分类和筛选通知的类别元数据。

- 一个可选意图，指示点击通知时要启动的活动。

- 将在其上发布通知的通知通道的 ID （Android 8.0 及更高版本）。

在生成器中设置这些选项后，您将生成一个包含设置的通知对象。 若要发布通知，请将此通知对象传递到*通知管理器*。 Android 提供[NotificationManager](xref:Android.App.NotificationManager)类，该类负责发布通知并向用户显示通知。 可以从任何上下文（例如活动或服务）获取对此类的引用。

### <a name="creating-a-notification-channel"></a>创建通知通道

在 Android 8.0 上运行的应用必须创建通知通道。 通知通道需要以下三部分信息：

- 将标识信道的包唯一的 ID 字符串。
- 将向用户显示的频道的名称。  名称长度必须介于1到40个字符之间。
- 通道的重要性。

应用需要检查正在运行的 Android 版本。
运行早于 Android 8.0 的版本的设备不应创建通知通道。 以下方法是如何在活动中创建通知通道的一个示例：

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

每次创建活动时都应创建通知通道。 对于 `CreateNotificationChannel` 方法，应在活动的 `OnCreate` 方法中调用此方法。

### <a name="creating-and-publishing-a-notification"></a>创建和发布通知

若要在 Android 中生成通知，请执行以下步骤：

1. 实例化 `NotificationCompat.Builder` 对象。

2. 调用 `NotificationCompat.Builder` 对象上的各种方法以设置通知选项。

3. 调用 `NotificationCompat.Builder` 对象的[生成](xref:Android.App.Notification.Builder.Build)方法，实例化通知对象。

4. 调用通知管理器的[通知](xref:Android.App.NotificationManager.Notify*)方法以发布通知。

必须为每个通知至少提供以下信息：

- 小图标（大小为24x24 的 dp）

- 简短标题

- 通知文本

下面的代码示例演示如何使用 `NotificationCompat.Builder` 生成基本通知。 请注意，`NotificationCompat.Builder` 方法支持[方法链接](https://en.wikipedia.org/wiki/Method_chaining);也就是说，每个方法都返回生成器对象，以便您可以使用最后一个方法调用的结果来调用下一个方法调用：

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此示例中，将实例化名为 `builder` 的新 `NotificationCompat.Builder` 对象，以及要使用的通知通道的 ID。 设置通知的标题和文本，并从**资源/可绘制/ic_notification .png**加载通知图标。 对通知生成器的 `Build` 方法的调用将使用这些设置创建通知对象。 下一步是调用通知管理器的 `Notify` 方法。 如以上所示，若要查找通知管理器，请调用 `GetSystemService`。

`Notify` 方法接受两个参数：通知标识符和通知对象。 通知标识符是一个唯一的整数，用于标识应用程序的通知。 在此示例中，通知标识符设置为零（0）;但是，在生产应用程序中，您需要为每个通知指定一个唯一标识符。 在对 `Notify` 的调用中重用以前的标识符值会导致覆盖最后一个通知。

当此代码在 Android 5.0 设备上运行时，它将生成类似于以下示例的通知：

![示例代码的通知结果](local-notifications-images/09-hello-world.png)

通知图标显示在通知的左侧 &ndash; 此图像的已圆圈 &ldquo;i&rdquo; 具有 alpha 通道，以便 Android 可以在其后面绘制灰色的圆形背景。 还可以提供不带 alpha 通道的图标。 若要将照片图像显示为图标，请参阅本主题后面的[大图标格式](#large-icon-format)。

时间戳是自动设置的，但你可以通过调用通知生成器的[SetWhen](xref:Android.App.Notification.Builder.SetWhen*)方法来重写此设置。 例如，下面的代码示例将时间戳设置为当前时间：

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>启用声音和振动

如果希望通知也播放声音，可以调用通知生成器的[SetDefaults](xref:Android.App.Notification.Builder.SetDefaults*)方法，并传入 `NotificationDefaults.Sound` 标志：

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

对 `SetDefaults` 的此调用将导致设备在发布通知时播放声音。 如果你想要设备振动而不是播放声音，则可以将 `NotificationDefaults.Vibrate` 传递到 `SetDefaults.` 如果你希望设备播放声音并振动设备，则可以将这两个标志传递到 `SetDefaults`：

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

如果在不指定要播放的声音的情况下启用声音，Android 将使用默认系统通知声音。 但是，可以通过调用通知生成器的[SetSound](xref:Android.App.Notification.Builder.SetSound*)方法来更改要播放的声音。 例如，若要通过通知（而不是默认通知声音）播放警报声音，可以从[RingtoneManager](xref:Android.Media.RingtoneManager)获取警报声音的 URI 并将其传递给 `SetSound`：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

或者，你可以使用系统默认铃声声音作为通知：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

创建通知对象之后，可以在通知对象上设置通知属性（而不是通过 `NotificationCompat.Builder` 方法提前配置通知）。 例如，你可以直接修改通知的 "[默认值](xref:Android.App.Notification.Defaults)" 属性的位标志，而不是调用 `SetDefaults` 方法来启用对通知的振动：

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

此示例将在发布通知时导致设备振动。

### <a name="updating-a-notification"></a>更新通知

如果要在发布通知之后更新其内容，可以重复使用现有的 `NotificationCompat.Builder` 对象来创建新的通知对象，并使用上一通知的标识符发布此通知。 例如：

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

在此示例中，现有 `NotificationCompat.Builder` 对象用于创建新的通知对象，该对象具有不同的标题和消息。
使用之前通知的标识符发布新的通知对象，这将更新以前发布的通知的内容：

![已更新通知](local-notifications-images/12-updated-notification.png)

当通知在通知抽屉中显示时，将重新使用之前通知的正文 &ndash; 仅显示通知的标题和文本。 标题文本从 "示例通知" 更改为 "已更新通知"，消息文本将从 "Hello World！ 这是我的第一条通知！ " 更改为 "已更改为此消息"。

通知将保持可见，直到发生以下三种情况之一：

- 用户关闭通知（或点击 "*全部清除*"）。

- 应用程序调用 `NotificationManager.Cancel`，并传入发布通知时分配的唯一通知 ID。

- 应用程序调用 `NotificationManager.CancelAll`。

有关更新 Android 通知的详细信息，请参阅[修改通知](https://developer.android.com/training/notify-user/managing.html#Updating)。

### <a name="starting-an-activity-from-a-notification"></a>从通知启动活动

在 Android 中，通知与*操作*&ndash; 相关联，用户点击通知时就会启动活动。 此活动可以位于另一个应用程序中，也可以位于另一个任务中。 若要向通知添加操作，请创建一个[PendingIntent](xref:Android.App.PendingIntent)对象，并将该 `PendingIntent` 与通知相关联。 `PendingIntent` 是一种特殊的意图，允许收件人应用程序使用发送应用程序的权限运行预定义的代码段。 当用户点击通知时，Android 将启动 `PendingIntent`指定的活动。

下面的代码段演示了如何使用将启动原始应用程序的 `PendingIntent` 的通知创建通知 `MainActivity`：

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

此代码与上一部分中的通知代码非常相似，不同之处在于将 `PendingIntent` 添加到通知对象。 在此示例中，`PendingIntent` 与源应用的活动相关联，然后将其传递到通知生成器的[SetContentIntent](xref:Android.App.Notification.Builder.SetContentIntent*)方法。 `PendingIntentFlags.OneShot` 标志传递到 `PendingIntent.GetActivity` 方法，以便仅使用一次 `PendingIntent`。 此代码运行时，将显示以下通知：

![第一次操作通知](local-notifications-images/10-first-action-notification.png)

点击此通知会使用户返回到原始活动。

在生产应用程序中，当用户在通知活动中按 "**后退**" 按钮时，应用程序必须处理*back 堆栈*（如果你不熟悉 Android 任务和后退堆栈，请参阅[任务和后退堆栈](https://developer.android.com/guide/components/tasks-and-back-stack.html)）。
在大多数情况下，从通知活动向后导航时，应将用户从应用程序返回到主屏幕。 若要管理 back 堆栈，你的应用程序使用[TaskStackBuilder](xref:Android.App.TaskStackBuilder)类创建具有 back 堆栈的 `PendingIntent`。

需要考虑的另一个实际因素是：原始活动可能需要将数据发送到通知活动。 例如，通知可能指示文本消息已到达，通知活动（消息查看屏幕）需要消息的 ID 才能向用户显示该消息。 创建 `PendingIntent` 的活动可以使用[PutExtra](xref:Android.Content.Intent.PutExtra*)方法将数据（例如字符串）添加到目的，以便将此数据传递到通知活动。

下面的代码示例演示如何使用 `TaskStackBuilder` 来管理 back 堆栈，并提供一个示例，说明如何将单个消息字符串发送到称为 `SecondActivity`的通知活动：

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此代码示例中，应用由两个活动组成： `MainActivity` （其中包含上述通知代码）和 `SecondActivity`，即在点击通知后用户看到的屏幕。 此代码运行时，将显示一个简单的通知（与前面的示例类似）。 点击通知会使用户进入 `SecondActivity` 屏幕：

![第二个活动屏幕快照](local-notifications-images/11-second-activity.png)

通过下面这行代码在 `SecondActivity` 中检索字符串消息（传递到目的的 `PutExtra` 方法）：

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

此检索到的消息 "`SecondActivity`" 屏幕中显示 "问候 from！"，如上面的屏幕截图中所示。 当用户在 `SecondActivity`中按 "**后退**" 按钮时，导航将从应用程序开始，并在启动应用程序之前返回到屏幕。

有关创建挂起意向的详细信息，请参阅[PendingIntent](xref:Android.App.PendingIntent)。

<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>基本通知外

通知默认为 Android 中的简单基本布局格式，但你可以通过进行其他 `NotificationCompat.Builder` 方法调用来增强此基本格式。 在本部分中，你将学习如何向通知添加大照片图标，并将看到有关如何创建扩展的布局通知的示例。

<a name="large-icon-format" />

### <a name="large-icon-format"></a>大图标格式

Android 通知通常显示原始应用的图标（位于通知左侧）。 但是，通知可以显示图像或照片（*大图标*），而不是标准的小图标。 例如，消息应用可以显示发送方的照片，而不是应用图标。

下面是一个基本 Android 5.0 通知的示例，&ndash; 它仅显示小应用图标：

![示例普通通知](local-notifications-images/13-sample-notification.png)

下面是在修改后的通知屏幕截图，以显示大图标 &ndash; 它使用从 Xamarin 代码猴子的图像创建的图标：

![示例大图标通知](local-notifications-images/14-large-icon-sample.png)

请注意，当以大图标格式显示通知时，小型应用图标将显示为大图标右下角的标记。

若要在通知中将图像用作大图标，请调用通知生成器的[SetLargeIcon](xref:Android.App.Notification.Builder.SetLargeIcon*)方法，并传入图像的位图。 与 `SetSmallIcon`不同，`SetLargeIcon` 只接受位图。 若要将图像文件转换为位图，请使用[BitmapFactory](xref:Android.Graphics.BitmapFactory)类。 例如：

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

此示例代码在**资源/可绘制/monkey_icon .png**中打开图像文件，将其转换为位图，并将生成的位图传递到 `NotificationCompat.Builder`。 通常情况下，源图像分辨率大于小图标 &ndash; 但并不大。 太大的图像可能会导致不必要的调整大小操作，这些操作可能会延迟发送通知。

### <a name="big-text-style"></a>大文本样式

*大文本*样式是一个扩展的布局模板，用于在通知中显示长消息。 与所有展开的布局通知一样，大文本通知最初显示为精简的演示文稿格式：

![大文本通知示例](local-notifications-images/15-big-text-notification.png)

此格式只显示消息摘录，以两个句点结束。 当用户向下拖动通知时，它将展开以显示整个通知消息：

![扩展的大文本通知](local-notifications-images/16-big-text-expanded.png)

此扩展的布局格式还包括通知底部的摘要文本。 *大文本*通知的最大高度为 256 dp。

若要创建*大文本*通知，请像以前一样实例化 `NotificationCompat.Builder` 对象，然后实例化并将[BigTextStyle](xref:Android.App.Notification.BigTextStyle)对象添加到 `NotificationCompat.Builder` 对象。 以下是示例：

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

在此示例中，消息文本和摘要文本将存储在 `BigTextStyle` 对象（`textStyle`）中，然后将其传递到 `NotificationCompat.Builder.`

### <a name="image-style"></a>图像样式

*图像*样式（也称为 "*大图片*样式"）是一种扩展的通知格式，可用于在通知正文中显示图像。 例如，屏幕截图应用或照片应用可以使用*图像*通知样式向用户提供捕获的最后一个映像的通知。 请注意，*图像*通知的最大高度为 256 Dp &ndash; Android 将调整图像大小，使其适应此最大高度限制（在可用内存限制内）。

与所有展开的布局通知一样，*图像*通知首先以简洁格式显示，其中显示了伴随消息文本的摘录：

![压缩映像通知显示无图像](local-notifications-images/17-image-compact.png)

当用户在*图像*通知上向下拖动时，它将展开以显示图像。 例如，下面是之前通知的扩展版本：

![展开的图像通知显示图像](local-notifications-images/18-image-expanded.png)

请注意，当通知以 compact 格式显示时，它会显示通知文本（传递到通知生成器的 `SetContentText` 方法的文本，如前文所述）。 但是，当扩展通知以显示图像时，它将显示图像上方的摘要文本。

若要创建*图像*通知，请将 `NotificationCompat.Builder` 对象实例化为之前，然后创建[BigPictureStyle](xref:Android.App.Notification.BigPictureStyle)对象并将其插入到 `NotificationCompat.Builder` 对象中。 例如：

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

与 `NotificationCompat.Builder`的 `SetLargeIcon` 方法一样，`BigPictureStyle` 的[BigPicture](xref:Android.App.Notification.BigPictureStyle.BigPicture*)方法需要要在通知正文中显示的图像的位图。 在此示例中，的[DecodeResource](xref:Android.Graphics.BitmapFactory.DecodeResource*) `BitmapFactory` 方法将读取位于**资源//x_bldg .png**的映像文件，并将其转换为位图。

你还可以显示未打包为资源的映像。 例如，以下示例代码从本地 SD 卡加载映像，并在*映像*通知中显示它：

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

在此示例中，加载了位于 **/sdcard/Pictures/my-tshirt.jpg**的映像文件，将其大小调整为原始大小的一半，然后转换为位图以便在通知中使用：

![通知中的 T 恤图像示例](local-notifications-images/19-tshirt-notification.png)

如果事先不知道映像文件的大小，则最好在异常处理 &ndash; 程序中包装对[BitmapFactory](xref:Android.Graphics.BitmapFactory.DecodeFile*)的调用，如果图像太大而无法进行调整，则可能引发 `OutOfMemoryError` 异常。

有关加载和解码大位图图像的详细信息，请参阅[有效加载大位图](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently)。

### <a name="inbox-style"></a>收件箱样式

*收件箱*格式是一个扩展的布局模板，用于在通知正文中显示单独的文本行（如电子邮件收件箱摘要）。 *收件箱*格式通知首先以精简格式显示：

![压缩收件箱通知示例](local-notifications-images/20-inbox-compact.png)

当用户向下拖动通知时，它将展开以显示电子邮件摘要，如以下屏幕截图中所示：

![展开的示例收件箱通知](local-notifications-images/21-inbox-expanded.png)

若要创建*收件箱*通知，请先实例化 `NotificationCompat.Builder` 的对象，然后再将[InboxStyle](xref:Android.App.Notification.InboxStyle)对象添加到 `NotificationCompat.Builder`中。 以下是示例：

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

若要将新的文本行添加到通知正文，请调用 `InboxStyle` 对象的[Addline](xref:Android.App.Notification.InboxStyle.AddLine*)方法（*收件箱*通知的最大高度为 256 dp）。 请注意，与*大文本*样式不同的是，*收件箱*样式支持通知正文中的单个文本行。

你还可以将*收件箱*样式用于任何需要以展开格式显示单个文本行的通知。 例如，可以使用*收件箱*通知样式将多个待定通知合并为摘要通知 &ndash; 你可以使用新通知内容行更新单个*收件箱*样式通知（请参阅上面[的更新通知](#updating-a-notification)），而不是生成新的、主要类似通知的流。

## <a name="configuring-metadata"></a>配置元数据

`NotificationCompat.Builder` 包括一些方法，您可以调用这些方法来设置有关您的通知的元数据，如优先级、可见性和类别。 Android 使用此信息 &mdash; 和用户首选项设置 &mdash; 来确定显示通知的方式和时间。

### <a name="priority-settings"></a>优先级设置

在 Android 7.1 和更低版本上运行的应用程序需要直接在通知本身上设置优先级。 通知的优先级设置确定发布通知时的两个结果：

- 通知显示在与其他通知相关的位置。
    例如，在通知抽屉中，高优先级通知在优先级较低的通知的上方显示，而不考虑每个通知发布的时间。

- 通知是否以打印头通知格式（Android 5.0 及更高版本）显示。 只有*高*优先级和*最*高优先级的通知显示为打印头通知。

Xamarin 定义以下用于设置通知优先级的枚举：

- `NotificationPriority.Max` &ndash; 会提醒用户出现紧急或严重情况（例如，传入呼叫、轮流指示或紧急警报）。 在 Android 5.0 及更高版本的设备上，最高优先级通知以标头的格式显示。

- `NotificationPriority.High` &ndash; 通知用户重要的事件（例如重要的电子邮件或实时聊天消息的到达）。 在 Android 5.0 及更高版本的设备上，高优先级通知以打印头显示格式显示。

- `NotificationPriority.Default` &ndash; 向用户通知具有中等重要性级别的条件。

- 为用户需要通知的非紧急信息 `NotificationPriority.Low` &ndash; （例如，软件更新提醒或社交网络更新）。

- `NotificationPriority.Min` 在查看通知（例如，位置或天气信息）时用户仅注意到的背景信息 &ndash;。

若要设置通知的优先级，请调用 `NotificationCompat.Builder` 对象的[SetPriority](xref:Android.App.Notification.Builder.SetPriority*)方法，并传入优先级别。 例如：

```csharp
builder.SetPriority (NotificationPriority.High);
```

在下面的示例中，"重要消息！" 的高优先级通知 出现在通知抽屉顶部：

![高优先级通知示例](local-notifications-images/22-hi-priority-drawer.png)

由于这是一个高优先级的通知，因此，它还会在 Android 5.0 的用户当前活动屏幕上显示为提醒通知：

![示例打印头通知](local-notifications-images/23-heads-up-example.png)

在下一个示例中，在更高优先级的电池级别通知下显示低优先级的 "考虑日期" 通知：

![低优先级通知示例](local-notifications-images/24-lo-priority-drawer.png)

由于 "思考日期" 通知是低优先级通知，Android 不会以标头的格式显示。

> [!NOTE]
> 在 Android 8.0 及更高版本中，通知通道和用户设置的优先级将决定通知的优先级。

### <a name="visibility-settings"></a>可见性设置

从 Android 5.0 开始，*可见性*设置可用于控制安全锁定屏幕上显示的通知内容的数量。
Xamarin 定义以下用于设置通知可见性的枚举：

- `NotificationVisibility.Public` &ndash; 在安全锁定屏幕上显示通知的完整内容。

- `NotificationVisibility.Private` &ndash; 安全锁定屏幕上只显示必要的信息（例如通知图标和发布的应用程序的名称），但其余通知的详细信息将隐藏。 所有通知默认为 `NotificationVisibility.Private`。

- &ndash; `NotificationVisibility.Secret` 不会在安全锁定屏幕上显示任何内容，甚至不会显示通知图标。 通知内容仅在用户解锁设备后可用。

若要设置通知的可见性，应用将调用 `NotificationCompat.Builder` 对象的 `SetVisibility` 方法，并传入可见性设置。 例如，对的此调用 `SetVisibility` 会使通知 `Private`：

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

发布 `Private` 通知时，安全锁定屏幕上只显示应用的名称和图标。 用户会看到 "解锁设备以查看此通知"，而不是通知消息：

![解锁设备通知消息](local-notifications-images/25-lockscreen-private.png)

在此示例中， **NotificationsLab**是原始应用程序的名称。 此修正版本的通知仅在锁定屏幕安全时显示（即通过 PIN、模式或密码保护） &ndash; 如果锁定屏幕不安全，通知屏幕上会提供通知的完整内容。

### <a name="category-settings"></a>类别设置

从 Android 5.0 开始，预定义的类别可用于排名和筛选通知。 Xamarin 提供以下类别的枚举：

- &ndash; 传入电话呼叫 `Notification.CategoryCall`。

- &ndash; 传入文本消息 `Notification.CategoryMessage`。

- `Notification.CategoryAlarm` &ndash; 警报条件或计时器过期。

- `Notification.CategoryEmail` &ndash; 传入电子邮件。

- `Notification.CategoryEvent` &ndash; 日历事件。

- `Notification.CategoryPromo` &ndash; 促销消息或播发。

- `Notification.CategoryProgress` &ndash; 后台操作的进度。

- `Notification.CategorySocial` &ndash; 社交网络更新。

- `Notification.CategoryError` 后台操作或身份验证过程 &ndash; 失败。

- `Notification.CategoryTransport` &ndash; Media 播放更新。

- &ndash; 保留给系统使用（系统或设备状态） `Notification.CategorySystem`。

- `Notification.CategoryService` &ndash; 指示后台服务正在运行。

- `Notification.CategoryRecommendation` &ndash; 与当前正在运行的应用程序相关的建议消息。

- `Notification.CategoryStatus` 有关设备的 &ndash; 信息。

对通知进行排序时，通知的优先级优先于其类别设置。 例如，高优先级通知显示为 "已启动" 状态，即使它属于 "`Promo`" 类别也是如此。 若要设置通知的类别，请调用 `NotificationCompat.Builder` 对象的 `SetCategory` 方法，并传入类别设置。 例如：

```csharp
builder.SetCategory (Notification.CategoryCall);
```

"*请勿打扰*" 功能（Android 5.0 中的新增功能）根据类别筛选通知。 例如，"**设置**" 中的 "不*打扰*" 屏幕允许用户免除电话呼叫和消息的通知：

![请勿打扰屏幕开关](local-notifications-images/26-do-not-disturb.png)

当用户将 "请勿*打扰*" 配置为阻止除电话呼叫之外的所有中断（如上面的屏幕截图中所示）时，Android 允许在设备处于 "*请勿打扰*" 模式时显示类别设置为 "`Notification.CategoryCall`" 的通知。 请注意，在 "*请勿打扰*" 模式下，不会阻止 `Notification.CategoryAlarm` 通知。

[LocalNotifications](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)示例演示如何使用 `NotificationCompat.Builder` 从通知启动第二个活动。 本示例代码在[使用 Xamarin 演练中的本地通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)中进行了介绍。

### <a name="notification-styles"></a>通知样式

若要使用 `NotificationCompat.Builder`创建*大文本*、*图像*或*收件箱*样式通知，应用程序必须使用这些样式的兼容版本。 例如，若要使用 "*大文本*" 样式，请实例化 `NotificationCompat.BigTextstyle`：

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

同样，你的应用程序可以分别使用 *"收件箱" 和 "* *图像*" 样式 `NotificationCompat.InboxStyle` 和 `NotificationCompat.BigPictureStyle`。

### <a name="notification-priority-and-category"></a>通知优先级和类别

`NotificationCompat.Builder` 支持 `SetPriority` 方法（从 Android 4.1 开始提供）。 但 `NotificationCompat.Builder`*不*支持 `SetCategory` 方法，因为类别是 Android 5.0 中引入的新通知元数据系统的一部分。

为了支持较早版本的 Android，其中 `SetCategory` 不可用，你的代码可以在运行时检查 API 级别，以便在 API 级别等于或大于 Android 5.0 （API 级别21）时按条件调用 `SetCategory`：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

在此示例中，应用的**目标框架**设置为 android 5.0，**最低 android 版本**设置为**android 4.1 （API 级别16）** 。 由于 `SetCategory` 在 API 级别21和更高版本中可用，因此此示例代码仅在可用时调用 `SetCategory` &ndash; 当 API 级别小于21时，它将不会调用 `SetCategory`。

### <a name="lock-screen-visibility"></a>锁定屏幕可见性

由于 Android 在 Android 5.0 （API 级别21）之前不支持锁定屏幕通知，因此 `NotificationCompat.Builder` 不支持 `SetVisibility` 方法。 如上所述，在 `SetCategory`中，你的代码可以在运行时检查 API 级别并仅在可用时调用 `SetVisiblity`：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```

## <a name="summary"></a>总结

本文介绍了如何在 Android 中创建本地通知。 它介绍了通知剖析，并说明了如何使用 `NotificationCompat.Builder` 来创建通知，如何使用来创建通知，如何以大图标、*大文本*、*图像*和*收件箱*格式设置通知的样式、如何设置通知元数据设置（如优先级、可见性和类别）以及如何从通知启动活动。 本文还介绍了如何使用 Android 5.0 中引入的新的 "打印头更新"、"锁定屏幕" 和 "*请勿打扰*" 功能。 最后，你已了解如何使用 `NotificationCompat.Builder` 维护与早期版本 Android 的通知兼容性。

有关为 Android 设计通知的指导原则，请参阅[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)。

## <a name="related-links"></a>相关链接

- [NotificationsLab （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-notificationslab)
- [LocalNotifications （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Android 中的本地通知演练](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [通知用户](https://developer.android.com/training/notify-user/index.html)
- [提醒](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [Notificationcompat.builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
