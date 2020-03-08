---
title: iOS 10 简介
description: 本文介绍适用于 Xamarin iOS 开发人员的 iOS 10 中的所有新的和修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ce262faf2d79e6a2cc969df582446fdc2ec29bde
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910888"
---
# <a name="introduction-to-ios-10"></a>iOS 10 简介

使用新的 iOS 10 SDK，Apple 提供了新的 Api 和服务，使开发人员能够创建新类别的应用和功能。 IOS 应用现在可以扩展消息、Siri、手机和地图应用，为以前不可用的最终用户提供丰富的功能。

有关 iOS 10 的详细信息，请参阅 Apple 的[ios + 应用](https://developer.apple.com/ios/)文档。

## <a name="whats-new-in-ios-10"></a>IOS 10 中的新增功能

Apple 在 iOS 10 中添加了几个新的 Api 和服务，并对现有功能进行了许多增强，其中包括：

## <a name="adapting-to-the-true-tone-display"></a>适应真正的音调显示

Apple 的真正语气显示技术使用 iOS 设备中的环境光线传感器来动态调整显示器的颜色和强度，使其与当前照明条件相匹配。 iOS 10 提供了新的[UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31)密钥，可将其添加到应用的 `Info.plist` 文件中，并控制 True 声调应用标准颜色转换的方式。 

可用值如下：

- `UIWhitePointAdaptivityStyleStandard`**默认值**-使用标准的 adaptivity。
- `UIWhitePointAdaptivityStyleReading`-用于读取重点应用。
- `UIWhitePointAdaptivityStyleGame`-用于以游戏为中心的应用。
- `UIWhitePointAdaptivityStyleVideo`-用于面向视频的应用。
- `UIWhitePointAdaptivityStylePhoto`-适用于在颜色保真度比环境白点调整更重要的图片聚焦应用。

## <a name="app-extensions"></a>应用扩展

Apple 在 iOS 10 中提供了几个新的应用扩展点：

- 调用目录
- 意向和意向 UI
- 消息
- 通知内容
- Notification Services
- 不干胶标签

此外，第三方键盘应用扩展具有以下增强功能：

- `UITextDocumentProxy` 类的新 `DocumentInputMode` 属性可以确定文档的输入语言，并允许键盘扩展与该语言对齐。
- 新的 `HandleInputModeList` 方法使键盘扩展可以显示系统的键盘选取器菜单，以响应被攻入的地球键。

有关详细信息，请参阅我们的[扩展简介](~/ios/platform/extensions.md)、[消息应用集成](~/ios/platform/message-app-integration/index.md)、[前瞻性建议](~/ios/platform/search/proactive-suggestions.md)简介、 [SiriKit 简介](~/ios/platform/sirikit/index.md)[和 Apple](~/ios/platform/user-notifications/index.md) [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>应用搜索增强功能

IOS 10 中的核心聚焦为应用搜索提供了多项增强功能，例如：

- **众包深层链接的热门程度（带有差异隐私）** -提供一种在搜索结果中升级深层链接的应用内容的方法。
- **应用内搜索**-使用新的 `CSSearchQuery` 类提供应用内聚焦搜索功能，类似于邮件、邮件和便笺应用的工作方式。
- **搜索延续**-允许用户在聚焦或 Safari 中开始搜索，然后打开应用并继续搜索。
- **验证结果的可视化效果**-Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)现在会在执行测试时显示网站标记和深层链接的可视表示形式。
- **消息应用程序映像共享**-允许使用常用的应用程序内映像在消息中共享（通过消息应用扩展），以便在聚焦搜索中显示。

若要了解详细信息，请参阅[应用搜索增强](~/ios/platform/search/app-search-enhancements.md)指南。

## <a name="apple-pay-enhancements"></a>Apple Pay 增强功能

Apple 已在 iOS 10 中对 Apple Pay 进行了多项改进，使用户能够从网站中进行安全付款，并通过 Siri 和地图进行交互。

IOS 10 中添加了几个新的 Api，它们适用于 iOS 和 watchOS 以支持动态支付网络和新的沙箱测试环境。

此外，PassKit 框架已扩展为支持 `UIKit` 之外的 Apple Pay，并允许卡颁发商在其应用中提供其卡。

若要了解详细信息，请参阅我们的[Apple Pay 增强](~/ios/platform/apple-pay.md)指南。

## <a name="alternate-app-icons"></a>备用应用图标

Apple 向 iOS 10.3 添加了几项增强功能，使应用程序可以管理其图标：

- `ApplicationIconBadgeNumber` 获取或设置 Springboard 中应用程序图标的徽章。
- `SupportsAlternateIcons`-如果 `true` 应用程序具有一组备用图标。
- `AlternateIconName`-返回当前选定的备用图标的名称，如果使用主图标，则为 `null`。
- `SetAlternameIconName`-使用此方法将应用的图标切换到给定的替代图标。

若要了解详细信息，请参阅我们的[备用应用图标](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)指南。

## <a name="introduction-to-callkit"></a>CallKit 简介

IOS 10 中的新 CallKit API 提供了一种方法，让 VOIP 应用与 iPhone UI 集成，并为最终用户提供熟悉的界面和体验。 使用此 API，用户可以从 iOS 设备的锁定屏幕查看并与 VOIP 呼叫交互，并使用 Phone 应用的 **"收藏夹"** 和 "**最近**" 视图管理联系人。

此外，CallKit API 提供了创建应用扩展的功能，可以将电话号码与名称（呼叫方 ID）关联，也可以告知系统应阻止某个号码（呼叫阻塞）。

若要了解详细信息，请参阅[Callkit 指南简介](~/ios/platform/callkit.md)。

## <a name="message-app-integration"></a>消息应用集成

iOS 10 允许在 Xamarin iOS 解决方案中包含消息应用扩展，该扩展与**Messages**应用集成并向用户提供新功能。 此扩展可以发送文本、不干胶标签、媒体文件和交互式消息。 提供两种类型的消息应用扩展：

- **不干胶标签**-包含用户可添加到消息中的不干胶标签集合。 无需编写任何代码即可创建不干胶标签。
- **IMessage 应用**-可在邮件应用中提供自定义用户界面，用于选择不干胶标签，输入文本，包括媒体文件（使用可选类型转换）以及创建、编辑和发送交互消息。

若要了解详细信息，请参阅我们的[消息应用集成](~/ios/platform/message-app-integration/index.md)指南。

## <a name="news-publisher-enhancements"></a>新闻出版商增强功能

对于 iOS 10，Apple 允许来自主要杂志和新组织的任何人来注册和产品，并将内容传递到 Apple News 应用。 若要了解详细信息，请参阅 Apple 的[新闻资源](https://newsresources.apple.com/)文档。

## <a name="providing-haptic-feedback"></a>提供 Haptic 反馈

在 iPhone 7 和 iPhone 7 Plus 上，Apple 包含了新的 haptics 响应，这些响应提供了用于以物理方式吸引用户的其他方式。 使用新的 tactile 反馈选项，使用户注意并强化其操作。

几个内置 UI 元素已经提供了 haptic 的反馈，如选取器、开关和滑杆。 iOS 10 现在添加了使用 `UIFeedbackGenerator` 类的具体子类以编程方式触发 haptics 的功能。

若要了解详细信息，请参阅我们[提供的 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)指南。

## <a name="proactive-suggestions"></a>主动建议

iOS 10 提供了新的方法，可让系统在适当的时间自动向用户提供有用的信息，从而促进应用程序的参与。 正如 iOS 9 提供的功能，可以使用聚光灯、移交和 Siri 建议以及 iOS 10 向应用添加深层搜索，应用程序可以从以下位置公开系统可向用户显示的功能：

- 应用切换器
- 锁定屏幕
- CarPlay
- 地图
- Siri 交互
- QuickType 建议

应用使用[NSUserActivity](xref:Foundation.NSUserActivity)、web 标记、核心聚焦、MapKit、Media Player 和 UIKit 等技术集合向系统公开此功能。

若要了解详细信息，请参阅[前瞻性建议指南简介](~/ios/platform/search/proactive-suggestions.md)。

## <a name="request-app-review"></a>请求应用评审

新的 iOS 10.3，`RequestReview()` 方法允许 iOS 应用要求用户对其进行评级或查看。 尽管此方法可以在用户体验中有意义的任何位置调用，但是审核过程由应用商店策略控制和处理。 因此，此方法可能会也可能不会显示警报，并且决不会为响应用户操作（如点击按钮）而调用。

若要了解详细信息，请参阅[请求应用审阅](~/ios/platform/request-app-review.md)指南。

## <a name="security-and-privacy-enhancements"></a>安全和隐私增强

Apple 在 iOS 10 中对安全和隐私进行了多项改进，有助于开发人员提高应用程序的安全性并确保最终用户的隐私。

因此，在 iOS 10 （或更高版本）上运行的应用程序必须通过在其 `Info.plist` 文件中输入一个或多个特定于隐私的密钥，向用户说明应用希望获取访问权限的原因，以静态方式声明其意图以访问特定功能或用户信息。

若要了解详细信息，请参阅[安全和隐私增强](~/ios/app-fundamentals/security-privacy.md)指南。

## <a name="sirikit"></a>SiriKit

SiriKit 的新功能允许 Xamarin iOS 应用提供在 iOS 设备上使用 Siri 的用户可访问的服务。 此功能在一个或多个使用新的**意图**和**意向 UI**框架的应用扩展中提供。

SiriKit 支持以下服务域：

- 音频或视频呼叫。
- 预订一本。
- 管理 workouts。
- 彩信.
- 搜索照片。
- 发送或接收付款。

当用户发出涉及某个应用扩展服务的 Siri 请求时，SiriKit 将向该扩展发送**意向**对象，该对象描述用户的请求以及所有支持数据。 然后，应用扩展会针对给定**意向**生成相应的**响应**对象，并详细说明扩展如何处理请求。

尽管 Siri 通常处理所有用户交互，但应用扩展可使用**意向 UI**框架提供丰富的自定义用户界面，其中包含应用的品牌和其他信息。

若要了解详细信息，请参阅[SiriKit 指南简介](~/ios/platform/sirikit/index.md)。

## <a name="speech-recognition"></a>语音识别

iOS 10 包括一个新的语音 API，该 API 允许应用程序支持连续语音识别和转录语音（从实时或录制的音频流）到文本。

由于语音识别需要在 Apple 服务器上传输和临时存储数据，因此应用程序_必须_通过将 `NSSpeechRecognitionUsageDescription` 密钥包含在其 `Info.plist` 文件中并调用 `SFSpeechRecognizer.RequestAutorization` 方法来请求用户的权限来执行识别。

若要了解详细信息，请参阅[语音识别指南简介](~/ios/platform/speech.md)。

## <a name="user-notifications"></a>用户通知

用户通知框架是 iOS 10 的新功能，它允许传递和处理本地和远程通知。 使用此框架，应用或应用扩展可以通过指定一组条件（如位置或一天的时间）来计划本地通知的传送。

此外，在将本地和远程通知发送到用户的 iOS 设备时，该应用或扩展可以接收（并可能修改）这些通知。

新用户通知 UI 框架允许应用或应用扩展在向用户显示本地和远程通知时，对其外观进行自定义。

若要了解详细信息，请参阅我们的[用户通知框架](~/ios/platform/user-notifications/index.md)指南。

## <a name="video-subscriber-account"></a>视频订户帐户

适用于 iOS 10 的新功能，视频订户帐户框架允许支持经过身份验证的流式处理或视频点播的应用使用其有线或卫星电视提供商对最终用户进行身份验证。

## <a name="wide-color"></a>宽域颜色

iOS 10 在整个系统（包括核心图形、核心图像、金属和 AVFoundation 等框架）中扩展了对扩展范围像素格式和宽色域颜色空间的支持。 通过在整个图形堆栈中提供此行为，可进一步减轻对具有宽颜色显示的设备的支持。

此外， [UIKit](xref:UIKit)已修改为可在新的扩展**sRGB** colorspace 中使用，从而可以更轻松地混合使用宽颜色 gamuts 中的颜色，而不会显著降低性能。

当使用宽色时，Apple 提供以下最佳做法：

- [UIColor](xref:UIKit.UIColor)现在使用 sRGB 颜色空间，并且不再将值夹具到 `0.0` 到 `1.0` 范围。 如果应用依赖于以前的夹具行为，则需要为 iOS 10 进行修改。
- 在 iPad Pro 上执行自定义 `UIView` 绘图时，将为 sRGB 颜色空间配置绘图环境。
- 如果应用执行 `UIImages`的自定义呈现，则使用新的[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)类指定扩展范围或标准范围格式的使用。
- 当使用低级别 API （如核心图形或金属图）来提供图像处理时，开发人员应使用支持16位浮点值的扩展范围颜色空间和像素格式。 如果需要，开发人员必须手动固定颜色分量值。
- 核心图形、核心图像和金属绩效着色器都提供了在两个颜色空间之间进行转换的新方法。

若要了解详细信息，请参阅[宽颜色指南简介](~/ios/platform/wide-color.md)。

## <a name="widget-enhancements"></a>小组件增强功能

Apple 为小组件系统引进了几项增强功能，以确保小组件在新的 iOS 10 锁屏上存在的任何背景上看起来很出色。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)属性已弃用，并已替换为新的[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)属性。 此外，小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性，该属性允许开发人员描述可用内容的数量，并允许用户展开和折叠内容。

若要了解详细信息，请参阅我们的[搜索和主屏幕小组件增强](~/ios/platform/search/widgets.md)指南。

## <a name="additional-framework-changes"></a>其他框架更改

除了上面列出的主要框架更改和添加以外，Apple 还在 iOS 10 中进行了许多其他的次要框架更改。

若要了解详细信息，请参阅我们的[其他框架更改](~/ios/platform/introduction-to-ios10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>弃用的 API

IOS 10 中已弃用以下 Api：

- `CKDiscoverAllContactsOperation`、`CKDiscoveredUserInfo`、`CKDiscoverUserInfosOperation` 和 `CKFetchRecordChangesOperation` 类在 iOS 10 的 CloudKit 中已被弃用。 改用[CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation)、 [CKUserIdentity](xref:CloudKit.CKUserIdentity)和[CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation)类（支持记录共享）。
- 已不推荐使用几个[CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) api （例如，基于区域和基于查询的订阅）。 改用[CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription)和[CKQuerySubscription](xref:CloudKit.CKQuerySubscription) api。
- 与普遍内容相关的[NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)符号已被弃用。
- [UIViewController](xref:UIKit.UIViewController)类中的 `ADBannerView`、`ADInterstitialAd` 和相关符号已被弃用。
- 与浮点值相关的[SKUniform](https://developer.apple.com/reference/spritekit/skuniform)符号已被弃用。
- UIKit 的 `UILocalNotification`、`UIMutableUserNotificationAction`、`UIMutableUserNotificationCategory`、`UIUserNotificationAction`、`UIUserNotificationCategory` 和 `UIUserNotificationSettings` 类已被弃用。 请改用[用户通知](#user-notifications)框架。
- `HandleActionForLocalNotification`、`HandleActionForRemoteNotification`、`DidReceiveLocalNotification` 和 `DidReceiveRemoteNotification` WatchKit 方法已弃用。 改用 `HandleActionForNotification` 和 `DidReceiveNotification` 方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)的 `DidReceiveLocalNotification` 和 `DidReceiveRemoteNotification` 方法已弃用。 创建一个[UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate)实例，用于实现适当的方法，并将其分配给[UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)对象的 `Delegate` 属性。
- **Game Center 应用**已弃用，并已从 iOS 中删除。 如果应用使用 Gamekit\，它_必须_提供其自己的接口以显示 gamekit\ 功能，如排行榜等。

有关弃用功能的完整列表，请参阅 Apple 的[ios 9.3 到 iOS 10.0 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html)文档。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
