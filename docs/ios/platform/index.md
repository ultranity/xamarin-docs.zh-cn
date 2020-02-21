---
title: iOS 平台功能概述
description: 本文档链接到各种指南，这些指南介绍了不同版本的 iOS 中引入的功能和其他 iOS 平台功能。
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: bcf293b29d6ddca10ab60ae061491b60f1e30520
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495635"
---
# <a name="ios-platform-features-overview"></a>iOS 平台功能概述

此页列出了最新的 iOS 版本，并突出显示了你可以使用 Xamarin 访问的某些 Apple framework。

## <a name="ios-releases"></a>iOS 版本

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [IOS 13 简介](~/ios/platform/ios13/index.md) | 本文档介绍 Xamarin （iOS）。|
| [iOS 12 简介](~/ios/platform/introduction-to-ios12/index.md) | 本文档介绍生成 Xamarin iOS 应用程序时可使用的 iOS 12 功能。|
| [iOS 11 简介](~/ios/platform/introduction-to-ios11/index.md) | 本文档介绍 iOS 11 和 Xcode 9 中的新增功能和更新功能，如 ARKit、Core ML、Core NFC、拖放、MapKit、PDFKit、SiriKit 和视觉。 它链接到介绍如何将这些功能与 Xamarin 一起使用的指南。 |
| [iOS 10 简介](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 包括几个新的 Api 和服务，可让你开发具有新特性和功能的应用程序。 使用 iOS 10，应用具有新功能，例如扩展地图、消息、手机和 Siri。 本部分演示了如何在 Xamarin iOS 应用程序中利用这些功能的方式。 |
| [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)   | 本部分定义了从 iOS 8 升级时在 iOS 9 中进行的更改，以及如何在 Xamarin iOS 应用中使用这些功能。 |
| [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)         | iOS 8 对 iOS 7 中的操作系统进行了大量更改。 在这里，我们将演示它们的定义以及如何使用它们。 |
| [iOS 7 简介](~/ios/platform/introduction-to-ios7/index.md)   | 关于 iOS 7 中引入的主要新 Api，包括视图控制器转换、UIView 动画、UIKit Dynamics 和文本工具包的增强功能。 |
| [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)   | IOS 6 中引入的功能的说明，包括集合视图、传递工具包、事件工具包和社交框架。 |

## <a name="apple-pay"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay 随 iOS 8 一起引入，使用户能够通过其 iOS 设备为物品（如食物、娱乐和成员资格）付费。 它在 iPhone 6 和 iPhone 6 Plus 上可用，还可以与用于商店内购买的 Apple Watch 配对。 当在 iPhone 上使用时，它将使用触控 ID 作为向用户的信用卡或借记卡确认和授权交易的一种方法。

## <a name="callkit"></a>[CallKit](~/ios/platform/callkit.md)

IOS 10 中的新 CallKit API 提供了一种方法，让 VOIP 应用与 iPhone UI 集成，并为最终用户提供熟悉的界面和体验。 使用此 API，用户可以从 iOS 设备的锁屏界面查看并与 VOIP 呼叫交互，并使用 Phone 应用的 **"收藏夹" 和 "** **最近**" 视图管理联系人。

## <a name="contacts-and-contactsui"></a>[联系人和 ContactsUI](~/ios/platform/contacts.md)

引入 iOS 9 后，Apple 发布了两个新框架（`Contacts` 和 `ContactsUI`），用来替换 iOS 8 及更早版本使用的现有通讯簿和通讯簿 UI 框架。

## <a name="document-picker"></a>[文档选取器](~/ios/platform/document-picker.md)

文档选取器允许在应用之间共享文档。 这些文档可以存储在 iCloud 或其他应用的目录中。 文档通过用户在其设备上安装的一组[文档提供程序扩展](~/ios/platform/extensions.md)来共享。

## <a name="eventkit"></a>[EventKit](~/ios/platform/eventkit.md)

iOS 中内置了两个日历相关的应用程序：日历应用程序和提醒应用程序。 它非常简单，足以了解日历应用程序管理日历数据的方式，但提醒应用程序不太明显。 提醒实际上可能会有与这些日期相关联的日期、完成时间，等等。因此，iOS 会将所有日历数据（无论是日历事件还是提醒）存储在一个位置（称为*日历数据库*）中。

## <a name="ios-extensions"></a>[iOS 扩展](~/ios/platform/extensions.md)

IOS 8 中引入的扩展是由 iOS 在诸如**通知中心**内的标准上下文中提供的专用 `UIViewControllers`，这是由用户请求的自定义键盘类型来执行专用输入或其他上下文（例如编辑照片，其中扩展可提供特殊效果筛选器）。

## <a name="graphics-and-animation-in-ios"></a>[IOS 中的图形和动画](~/ios/platform/graphics-animation-ios/index.md)

IOS 中的图形和动画涵盖了 iOS 中的核心图形概念，如 CoreImage、核心图形和核心动画。

## <a name="handoff"></a>[Handoff](~/ios/platform/handoff.md)

Apple 在 iOS 8 和 OS X Yosemite （10.10）中引入了移交，以提供一种通用机制，使用户能够将其设备上启动的活动传输到另一台运行相同应用程序的设备，或其他支持相同活动的应用程序。

## <a name="healthkit"></a>[HealthKit](~/ios/platform/healthkit.md)

运行状况工具包提供了一个安全的数据存储，用于存储用户的与运行状况相关的信息。 运行状况工具包应用程序可以拥有用户的显式权限，读取和写入此数据存储并在添加相关数据时接收通知。 应用可以显示数据，或者用户可以使用 Apple 提供的运行状况应用来查看其所有数据的仪表板。

## <a name="homekit"></a>[HomeKit](~/ios/platform/homekit.md)

Apple 在 iOS 8 中引入了 HomeKit，以提供用于在用户的家庭中发现和与家庭自动化设备通信的通用框架。 HomeKit 提供了一个用于配置设备和设置操作以控制设备的常用平台。

## <a name="in-app-purchasing"></a>[应用内购买](~/ios/platform/in-app-purchasing/index.md)

iOS 应用程序可以使用 StoreKit 销售数字产品或服务– iOS 提供的一组 Api，可与 Apple 的服务器通信，通过其 Apple ID 向用户执行财务交易。 StoreKit Api 主要涉及检索产品信息和执行事务–没有用户界面组件。 实现应用内购买的应用程序必须构建自己的用户界面，并通过自定义代码跟踪购买的项目，为用户提供所需的产品或服务。

## <a name="ios-gaming-apis"></a>[iOS 游戏 Api](~/ios/platform/gaming/index.md)

Apple 在 iOS 9 中对游戏 Api 做出了多项技术改进，使 Xamarin iOS 应用中的游戏图形和音频更容易实现。 其中包括通过高级框架的轻松开发，并利用 iOS 设备 GPU 的强大功能提高速度和图形性能。

## <a name="message-app-integration"></a>[消息应用集成](~/ios/platform/message-app-integration/index.md)

新的 iOS 10，消息应用扩展与**Messages**应用集成并向用户提供新功能。 此扩展可以发送文本、不干胶标签、媒体文件和交互式消息。

## <a name="multitasking-for-ipad"></a>[适用于 iPad 的多任务](~/ios/platform/multitasking.md)

iOS 9 增加了对在特定 iPad 硬件上同时运行两个应用程序的多任务支持。 通过以下功能支持 iPad 的多任务： "滑过"、"拆分" 视图 & 图片中的图片。

## <a name="passkit"></a>[PassKit](~/ios/platform/passkit.md)

Passbook 是适用于 Iphone 和 iPod 的应用，适用于 iOS 6。 它存储并显示条形码以及其他信息，以链接其手机上的客户交易与 "真实世界"。 传递由商家生成，并通过电子邮件、Url 或商家自己的 iOS 应用发送给客户。 Passbook 在手机上存储和组织所有刀路，并根据日期/时间或设备位置在锁定屏幕上显示 "Pass" 提醒。

本文档介绍 Passbook，将 Pass 工具包 API 与 Xamarin 一起使用，并讨论如何在服务器上实现 Pass。

## <a name="photokit"></a>[PhotoKit](~/ios/platform/photokit.md)

照片工具包是一个新的框架，它允许应用程序查询系统映像库并创建自定义用户界面来查看和修改其内容。 它包括多种类，这些类表示图像和视频资产，以及资产（如影集和文件夹）的集合。

## <a name="request-app-review"></a>[请求应用评审](~/ios/platform/request-app-review.md)

新的 iOS 10.3，`RequestReview()` 方法允许 iOS 应用要求用户对其进行评级或查看。 在用户已从应用商店安装的装运应用中调用此方法时，iOS 10 将处理开发人员的整个评级和审核过程。 由于此过程由应用商店策略控制，因此可能会显示警报，也可能不显示。

## <a name="search-apis"></a>[搜索 API](~/ios/platform/search/index.md)

已在 iOS 9 中展开搜索，以提供更好的新方法来访问 Xamarin iOS 应用中的信息和功能。 使用新的应用搜索 Api，可通过聚焦和 Safari 搜索结果、移交和 Siri 提醒和建议使应用内容可供搜索。 这样，用户就可以快速访问应用中的活动和信息。

## <a name="sirikit"></a>[SiriKit](~/ios/platform/sirikit/index.md)

使用 iOS 10 的新功能，SiriKit 允许 iOS 应用提供使用 Siri 的用户可访问的服务，以及使用应用扩展**和新的**方法和**意向 UI**框架在 iOS 设备上映射应用的服务。

## <a name="social-framework"></a>[社交框架](~/ios/platform/social-framework.md)

社交框架提供了一个统一的 API，用于与中国的用户（包括_Twitter_和_Facebook_）以及_SinaWeibo_的社交网络交互。

## <a name="speech-recognition"></a>[语音识别](~/ios/platform/speech.md)

iOS 10 包括一个新的语音 API，该 API 允许应用程序支持连续语音识别和转录语音（从实时或录制的音频流）到文本。

## <a name="textkit"></a>[TextKit](~/ios/platform/textkit.md)

文本工具包是一个提供强大文本布局和呈现功能的新 API。 它构建在低级别核心文本框架之上，但比核心文本更易于使用。

## <a name="3d-touch"></a>[3D Touch](~/ios/platform/3d-touch.md)

本文将介绍如何使用新的 3D Touch Api 向在新的 iPhone 6s 和 iPhone 6s Plus 设备上运行的 Xamarin 应用程序添加压力敏感度。

## <a name="touch-id-and-face-id-with-xamarinios"></a>[具有 Xamarin 的 Touch ID 和人脸 ID](~/ios/platform/touch-id-face-id.md)

Touch ID 和人脸 ID 是自 iOS 8 以后可用的生物识别身份验证系统。 本文和示例介绍了如何将 Touch ID 和面部 ID 与 Xamarin 一起使用。

## <a name="user-notifications"></a>[用户通知](~/ios/platform/user-notifications/index.md)

用户通知框架是 iOS 10 的新功能，它允许传递和处理本地和远程通知。 使用此框架，应用或应用扩展可以通过指定一组条件（如位置或一天的时间）来计划本地通知的传送。

## <a name="wide-color"></a>[广泛的颜色](~/ios/platform/wide-color.md)

iOS 10 和 macOS Sierra 增强了对整个系统（包括核心图形、核心图像、金属和 AVFoundation 等框架）中的扩展范围像素格式和宽色域颜色空间的支持。 通过在整个图形堆栈中提供此行为，可进一步减轻对具有宽颜色显示的设备的支持。

## <a name="binding-objective-c"></a>[绑定 Objective-C](binding-objective-c/index.md)

使用 iOS 时，可能会遇到需要使用第三方目标-C 库的情况。 在这些情况下，可以使用 Monotouch.dialog 的绑定项目创建到本机C#目标 C 库的绑定。 该项目使用的工具与用于引入 iOS Api 的工具相同C#。 本文档介绍如何绑定目标 C Api。

## <a name="bind-ios-swift-libraries"></a>[绑定 iOS Swift 库](binding-swift/index.md)

本文档介绍如何创建C#对 Swift 代码的绑定，使其能够在 Xamarin iOS 应用程序中使用本机库和 CocoaPods。

## <a name="referencing-native-libraries"></a>[引用本机库](native-interop.md)

Xamarin.iOS 支持本机 C 库和 Objective-C 库链接。 本文档介绍如何将本机 C 库与你的 Xamarin iOS 项目链接在一起。

## <a name="embedded-frameworks"></a>[嵌入式框架](embedded-frameworks.md)

说明如何在 Xamarin iOS 应用程序中嵌入客观-C 用户框架。
