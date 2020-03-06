---
title: 通过 Azure 通知中心和 Xamarin 发送和接收推送通知
description: 本文介绍如何使用 Azure 通知中心向 Xamarin 应用程序发送跨平台推送通知。
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
ms.openlocfilehash: 778f56ec844e2802c1e1bc783824d55218678761
ms.sourcegitcommit: e9d88587aafc912124b87732d81c3910247ad811
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78337290"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>通过 Azure 通知中心和 Xamarin 发送和接收推送通知

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

推送通知将来自后端系统的信息传送到移动应用程序。 Apple、Google 和其他平台都有自己的推送通知服务（PNS）。 利用 Azure 通知中心，你可以跨平台集中传递通知，使后端应用程序能够与单个集线器通信，这会将通知分发到每个平台特定的 PNS。

按照以下步骤将 Azure 通知中心集成到移动应用中：

1. [设置推送 Notification Services 和 Azure 通知中心](#set-up-push-notification-services-and-azure-notification-hub)。
1. [了解如何使用模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。
1. [创建跨平台 Xamarin 窗体应用程序](#xamarinforms-application-functionality)。
1. [为推送通知配置本机 Android 项目](#configure-the-android-application-for-notifications)。
1. [为推送通知配置本机 iOS 项目](#configure-ios-for-notifications)。
1. [使用 Azure 通知中心测试通知](#test-notifications-in-the-azure-portal)。
1. [创建用于发送通知的后端应用程序](#create-a-notification-dispatcher)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>设置推送 Notification Services 和 Azure 通知中心

将 Azure 通知中心与 Xamarin 移动应用集成类似于将 Azure 通知中心与 Xamarin 本机应用程序集成。 按照[使用 Azure 通知中心向 Xamarin 推送通知](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging)中的 Firebase 控制台步骤设置**FCM 应用程序**。 使用 Xamarin Android 教程完成以下步骤：

1. 定义在示例中使用的 Android 包名称，如 `com.xamarin.notifysample`。
1. 从 Firebase 控制台下载**google-services。** 你将在以后的步骤中将此文件添加到 Android 应用程序。
1. 创建 Azure 通知中心实例，并为其指定名称。 本文和示例使用 `xdocsnotificationhub` 作为中心名称。
1. 复制 FCM **Server 密钥**，并将其保存为 Azure 通知中心内**Google （GCM/FCM）** 下的**API 密钥**。

以下屏幕截图显示了 Azure 通知中心中的 Google 平台配置：

![Azure 通知中心 Google 配置的屏幕截图](azure-notification-hub-images/fcm-notification-hub-config.png "Azure 通知中心 Google 配置")

需要 macOS 计算机才能完成 iOS 设备的设置。 按照[使用 Azure 通知中心向 Xamarin IOS 推送通知](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file)中的初始步骤来设置 APNS。 使用 Xamarin iOS 教程完成以下步骤：

1. 定义 iOS 捆绑标识符。 本文和示例将 `com.xamarin.notifysample` 用作捆绑标识符。
1. 创建证书签名请求（CSR）文件，并使用它来生成推送通知证书。
1. 在 Azure 通知中心中的**Apple （APNS）** 下上传推送通知证书。

以下屏幕截图显示了 Azure 通知中心的 Apple 平台配置：

![Azure 通知中心 Apple 配置的屏幕截图](azure-notification-hub-images/apns-notification-hub-config.png "Azure 通知中心 Apple 配置")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>将模板和标记注册到 Azure 通知中心

Azure 通知中心要求移动应用程序向中心注册、定义模板并订阅标记。 注册将平台特定的 PNS 句柄链接到 Azure 通知中心内的标识符。 若要了解有关注册的详细信息，请参阅[注册管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。

模板允许设备指定参数化消息模板。 可以按每个设备、每个标记自定义传入消息。 若要了解有关模板的详细信息，请参阅[模板](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

标记可用于订阅消息类别，如新闻、体育和天气。 为简单起见，示例应用程序将定义一个默认模板，其中包含一个名为 `messageParam` 的参数和一个名为 `default`的标记。 在更复杂的系统中，用户特定标记可用于在设备上向用户发送个性化通知。 若要了解有关标记的详细信息，请参阅[路由和标记表达式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。

若要成功接收消息，每个本机应用程序必须执行以下步骤：

1. 从平台 PNS 获取 PNS 句柄或令牌。
1. 向 Azure 通知中心注册 PNS 句柄。
1. 指定包含与传出消息相同参数的模板。
1. 订阅传出消息的目标标记。

在为通知[配置 Android 应用程序](#configure-the-android-application-for-notifications)和[为通知配置 iOS](#configure-ios-for-notifications)部分中的每个平台，详细介绍了这些步骤。

## <a name="xamarinforms-application-functionality"></a>Xamarin. Forms 应用程序功能

示例 Xamarin 窗体应用程序显示一系列推送通知消息。 这是通过 `AddMessage` 方法实现的，该方法将指定的推送通知消息添加到 UI。 此方法还可防止将重复的消息添加到 UI，并在主线程上运行，以便可以从任何线程调用它。 下面的代码演示了 `AddMessage` 方法：

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

该示例应用程序包含一个**AppConstants.cs**文件，该文件定义了平台项目使用的属性。 此文件需要通过 Azure 通知中心的值进行自定义。 以下代码显示了**AppConstants.cs**文件：

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

在 `AppConstants` 中自定义以下值，将示例应用程序连接到 Azure 通知中心：

* `NotificationHubName`：使用在 Azure 门户中创建的 Azure 通知中心的名称。
* `ListenConnectionString`：在 Azure 通知中心中的 "**访问策略**" 下找到此值。

以下屏幕截图显示了这些值在 Azure 门户中的位置：

![Azure 通知中心访问策略的屏幕截图](azure-notification-hub-images/notification-hub-access-policy.png "Azure 通知中心访问策略")

## <a name="configure-the-android-application-for-notifications"></a>配置 Android 应用程序以获取通知

完成以下步骤以配置 Android 应用程序以接收和处理通知：

1. 在 Firebase 控制台中配置 Android**包名称**以匹配包名称。
1. 安装以下 NuGet 包，以便与 Google Play Firebase 和 Azure 通知中心进行交互：
    1. GooglePlayServices。
    1. Firebase。
    1. Xamarin.Azure.NotificationHubs.Android.
1. 将在 FCM 安装过程中下载的 `google-services.json` 文件复制到项目中，并将生成操作设置为 `GoogleServicesJson`。
1. [配置 androidmanifest.xml 以与 Firebase 进行通信](#configure-android-manifest)。
1. [重写 FirebaseMessagingService 以处理消息](#override-firebasemessagingservice-to-handle-messages)。
1. [向 XAMARIN UI 添加传入通知](#add-incoming-notifications-to-the-xamarinforms-ui)。

> [!NOTE]
> **GoogleServicesJson**生成操作是**GooglePlayServices** NuGet 包的一部分。 Visual Studio 2019 在启动过程中设置可用的生成操作。 如果看不到**GoogleServicesJson**作为生成操作，请在安装 NuGet 包后重启 Visual Studio 2019。

### <a name="configure-android-manifest"></a>配置 Android 清单

`application` 元素中的 `receiver` 元素使应用能够与 Firebase 通信。 `uses-permission` 元素允许应用处理消息，并将其注册到 Azure 通知中心。 完整的**androidmanifest.xml**应类似于以下示例：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="override-firebasemessagingservice-to-handle-messages"></a>重写 FirebaseMessagingService 以处理消息

若要注册 Firebase 并处理消息，请将 `FirebaseMessagingService` 类的子类。 示例应用程序定义了一个子类 `FirebaseMessagingService``FirebaseService` 类。 此类标记有 `IntentFilter` 特性，其中包括 `com.google.firebase.MESSAGING_EVENT` 筛选器。 此筛选器允许 Android 将传入消息传递给此类以进行处理：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

当应用程序启动时，Firebase SDK 将从 Firebase 服务器自动请求一个唯一标记标识符。 成功请求后，将对 `FirebaseService` 类调用 `OnNewToken` 方法。 示例项目将重写此方法，并将令牌注册到 Azure 通知中心：

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

`SendRegistrationToServer` 方法将设备注册到 Azure 通知中心，并使用模板订阅标记。 示例应用程序定义了一个名为 `default` 的标记和一个在**AppConstants.cs**文件中名为 `messageParam` 的参数。 有关注册、标记和模板的详细信息，请参阅[在 Azure 通知中心注册模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。

接收到消息时，将对 `FirebaseService` 类调用 `OnMessageReceived` 方法：

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    base.OnMessageReceived(message);
    string messageBody = string.Empty;

    if (message.GetNotification() != null)
    {
        messageBody = message.GetNotification().Body;
    }

    // NOTE: test messages sent via the Azure portal will be received here
    else
    {
        messageBody = message.Data.Values.First();
    }

    // convert the incoming message to a local notification
    SendLocalNotification(messageBody);

    // send the incoming message directly to the MainPage
    SendMessageToMainPage(messageBody);
}

void SendLocalNotification(string body)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    intent.PutExtra("message", body);
    
    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new NotificationCompat.Builder(this)
        .SetContentTitle("XamarinNotify Message")
        .SetSmallIcon(Resource.Drawable.ic_launcher)
        .SetContentText(body)
        .SetAutoCancel(true)
        .SetShowWhen(false)
        .SetContentIntent(pendingIntent);

    if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
    {
        notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
    }

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}

void SendMessageToMainPage(string body)
{
    (App.Current.MainPage as MainPage)?.AddMessage(body);
}
```

使用 `SendLocalNotification` 方法将传入消息转换为本地通知。 此方法创建一个新 `Intent`，并将消息内容以 `string` `Extra`的形式放置在 `Intent` 中。 当用户点击本地通知时，无论应用处于前台还是后台，都将启动 `MainActivity` 并通过 `Intent` 对象访问消息内容。

本地通知和 `Intent` 示例要求用户执行点击通知的操作。 当用户在应用程序状态发生更改之前应采取措施时，这是理想的做法。 但是，在某些情况下，你可能需要访问消息数据而无需用户操作。 前面的示例还将消息直接发送到具有 `SendMessageToMainPage` 方法的当前 `MainPage` 实例。 在生产环境中，如果对一种消息类型实现这两种方法，则当用户点击该通知时，`MainPage` 对象将收到重复的消息。

> [!NOTE]
> 如果 Android 应用程序在后台或前台运行，则它将仅接收推送通知。 若要在主 `Activity` 未运行时接收推送通知，你必须实现一种服务，该服务超出了此示例的范围。 有关详细信息，请参阅[创建 Android 服务](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>向 Xamarin UI 添加传入通知

`MainActivity` 类需要获取处理通知和管理传入消息数据的权限。 下面的代码演示完整的 `MainActivity` 实现：

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (!IsPlayServiceAvailable())
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

`Activity` 属性将应用程序 `LaunchMode` 设置为 `SingleTop`。 此启动模式告知 Android OS 仅允许此活动的单个实例。 使用此启动模式，传入的 `Intent` 数据将路由到 `OnNewIntent` 方法，该方法提取消息数据并通过 `AddMessage` 方法将其发送到 `MainPage` 实例。 如果你的应用程序使用不同的启动模式，则它必须以不同方式处理 `Intent` 数据。

`OnCreate` 方法使用名为 `IsPlayServiceAvailable` 的帮助器方法来确保设备支持 Google Play 服务。 不支持 Google Play 服务的仿真器或设备无法从 Firebase 接收推送通知。

## <a name="configure-ios-for-notifications"></a>为通知配置 iOS

配置 iOS 应用程序以接收通知的过程如下：

1. 在**info.plist**文件中配置**捆绑标识符**，使其与预配配置文件中使用的值匹配。
1. 将 "**启用推送通知**" 选项添加到**info.plist**文件。
1. 将**NotificationHubs** NuGet 包添加到项目。
1. [用 APNS 注册通知](#register-for-notifications-with-apns)。
1. [向 Azure 通知中心注册应用程序并订阅标记](#register-with-azure-notification-hub-and-subscribe-to-tags)。
1. [向 XAMARIN UI 添加 APNS 通知](#add-apns-notifications-to-xamarinforms-ui)。

以下屏幕截图显示了在 Visual Studio 中的**info.plist**文件中选择的 "**启用推送通知**" 选项：

![推送通知权限的屏幕截图](azure-notification-hub-images/push-notification-entitlement.png "推送通知权限")

### <a name="register-for-notifications-with-apns"></a>用 APNS 注册通知

必须重写**AppDelegate.cs**文件中的 `FinishedLaunching` 方法，以便注册远程通知。 注册因设备上使用的 iOS 版本的不同而异。 示例应用程序中的 iOS 项目将重写 `FinishedLaunching` 方法以调用 `RegisterForRemoteNotifications`，如下面的示例中所示：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>向 Azure 通知中心注册并订阅标记

如果设备已成功注册 `FinishedLaunching` 方法中的远程通知，则 iOS 将调用 `RegisteredForRemoteNotifications` 方法。 应重写此方法，以执行以下操作：

1. 实例化 `SBNotificationHub`。
1. 取消注册任何现有注册。
1. 将设备注册到通知中心。
1. 使用模板订阅特定标记。

有关注册设备、模板和标记的详细信息，请参阅[在 Azure 通知中心注册模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。 下面的代码演示如何注册设备和模板：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> 在没有网络连接的情况下，注册远程通知可能会失败。 您可以选择重写 `FailedToRegisterForRemoveNotifications` 方法来处理注册失败。

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>向 Xamarin UI 添加 APNS 通知

当设备收到远程通知时，iOS 将调用 `ReceivedRemoteNotification` 方法。 传入消息 JSON 转换为 `NSDictionary` 对象，`ProcessNotification` 方法从字典中提取值并将这些值发送到 Xamarin `MainPage` 实例中。 重写 `ReceivedRemoteNotifications` 方法以调用 `ProcessNotification`，如下面的代码所示：

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>Azure 门户中的测试通知

利用 Azure 通知中心，你可以检查应用程序是否可以接收测试消息。 通过通知中心的 "**测试发送**" 部分，可以选择目标平台并发送消息。 将**发送到标记表达式**设置为 `default` 会将消息发送到已注册 `default` 标记模板的应用程序。 单击 "**发送**" 按钮将生成一个报表，其中包含与该消息连接的设备数。 以下屏幕截图显示了 Azure 门户中的 Android 通知测试：

![Azure 通知中心测试消息的屏幕截图](azure-notification-hub-images/azure-notification-hub-test-send.png "Azure 通知中心测试消息")

### <a name="testing-tips"></a>测试提示

1. 在测试应用程序是否可以接收推送通知时，必须使用物理设备。 Android 和 iOS 虚拟设备可能未正确配置，无法接收推送通知。
1. 示例 Android 应用程序在颁发 Firebase 令牌时注册其令牌和模板。 在测试过程中，可能需要请求新令牌并向 Azure 通知中心重新注册。 强制执行此方法的最佳方式是清理项目、删除 `bin` 和 `obj` 文件夹，并从设备中卸载应用程序，然后重新生成并部署。
1. 推送通知流的许多部分都以异步方式执行。 这可能会导致未命中断点或按意外顺序命中断点。 使用设备或调试日志记录跟踪执行，而不中断应用程序流。 使用 `Constants`中指定的 `DebugTag` 筛选 Android 设备日志。
1. 当在 Visual Studio 中停止调试时，应用程序将强制关闭。 在调试过程中启动的任何消息接收器或其他服务将关闭，并且将不会响应消息事件。

## <a name="create-a-notification-dispatcher"></a>创建通知调度程序

使用 Azure 通知中心，后端应用程序可以将通知调度到跨平台的设备。 此示例演示了**NotificationDispatcher**控制台应用程序的通知分派。 应用程序包含**DispatcherConstants.cs**文件，该文件定义以下属性：

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

你必须配置**DispatcherConstants.cs**以匹配 Azure 通知中心配置。 `SubscriptionTags` 属性的值应与客户端应用程序中使用的值匹配。 `NotificationHubName` 属性是 Azure 通知中心实例的名称。 `FullAccessConnectionString` 属性是在通知中心**访问策略**中找到的访问密钥。 以下屏幕截图显示了 Azure 门户中 `NotificationHubName` 和 `FullAccessConnectionString` 属性的位置：

![Azure 通知中心名称和 FullAccessConnectionString 的屏幕截图](azure-notification-hub-images/notification-hub-full-access-policy.png "Azure 通知中心名称和 FullAccessConnectionString")

控制台应用程序循环遍历每个 `SubscriptionTags` 值，并使用 `NotificationHubClient` 类的实例将通知发送到订阅服务器。 下面的代码演示了控制台应用程序 `Program` 类：

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

运行示例控制台应用程序时，可以按空格键来发送消息。 如果客户端应用程序的配置正确，则运行客户端应用程序的设备应收到编号的通知。

## <a name="related-links"></a>相关链接

* [推送通知模板](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。
* [设备注册管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。
* [路由和标记表达式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。
* [Xamarin Azure 通知中心教程](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)。
* [Xamarin Azure 通知中心教程](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)。
