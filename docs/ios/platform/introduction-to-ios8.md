---
title: iOS 8 简介
description: 对于 iOS 8，Apple 已为激发和感到满意开发人员提供了很多的新框架和 Api。 在本指南中，我们将介绍这些新的 Api，并了解 iOS 8 对开发人员和用户的好处。
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 9353cffd924688058c1495b9258cc7f0e0ce7b82
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910816"
---
# <a name="introduction-to-ios-8"></a>iOS 8 简介

_对于 iOS 8，Apple 已为激发和感到满意开发人员提供了很多的新框架和 Api。在本指南中，我们将介绍这些新的 Api，并了解 iOS 8 对开发人员和用户的好处。_

iOS 7 直观地更改了用户和开发人员所期望的整个 iOS 用户界面，从第一个 iPhone 操作系统开始。 IOS 8 通过为开发人员提供很多框架，使用户能够从 iPhone 直接控制其生活的几乎每个方面。 例如，可以使用*HealthKit*分析运行状况和适用性、密码 Sal 使用*localauthentication.framework*的生物识别身份验证、*应用扩展*在第三方应用之间打开通信通道和*HomeKit* ，使你能够将你的房子变成未来。 

如果 iOS 7 与令用户有关，则 iOS 8 侧重于令开发人员，其中包含一系列这些好吃新工具。 

本指南介绍了适用于 Xamarin 开发人员的新 Api。  

此外，iOS 8 中已弃用了几个 Api，本文档末尾详细介绍了这些 Api。

## <a name="requirements"></a>要求

在 Visual Studio for Mac 中创建 iOS 8 应用需要以下各项：

- **Xcode 7 和 ios 8 或更高版本**–需要在开发人员的计算机上安装和配置 Apple 的最新 Xcode 和 ios api。
- **Visual Studio for Mac** –应在用户设备上安装和配置 Visual Studio for Mac 的最新版本。
- **ios 8 设备或模拟器**–运行最新版 ios 8 的 ios 设备用于测试。

## <a name="home-and-leisure"></a>家庭和休闲

iOS 8 已帮助将 Apple 牢固地植物，而 iOS 设备则通过使用 HomeKit 和 HealthKit 将其直接放入您家中的核心。 在本部分中，我们将介绍这两个新框架的工作原理，以及如何将它们集成到你的 Xamarin iOS 应用程序中。

## <a name="homekit"></a>HomeKit

从 iPhone 控制设备并不是一种新的技术应用;许多连接总部的产品可以通过 iOS 应用进行控制。 不过，HomeKit 现在通过为家庭自动化设备升级通用协议，并通过使公共 API 可供某些制造商（如 iHome、Philips 和 Honeywell）使用来进一步执行此操作。 对于用户而言，这意味着他们可以从一个应用程序内部无缝地控制其家庭的几乎每个方面。 它们对于知道它们使用的是 Philips 的色调灯泡或嵌套警报是不相关的。 用户还可以将大量智能主进程链接到 "场景"。

借助 HomeKit，第三方应用和 Siri 可以发现附件，并将其添加到其个人主页配置数据库，编辑和操作这些数据，以及与附件及其服务进行沟通以执行操作。

### <a name="configuration"></a>配置

下图显示了 HomeKit 附件配置的基本层次结构：

![](introduction-to-ios8-images/image1.png "This diagram shows the basic hierarchy of the configuration of HomeKit accessories")

若要开始 HomeKit，开发人员需要确保其预配配置文件已选择 HomeKit 服务。 Apple 还为开发人员提供了用于 Xcode 的 HomeKit 模拟器外接程序。 可在[Apple 开发人员中心](https://developer.apple.com/downloads/index.action)的 `Hardware IO Tools for Xcode`下找到此项。 

有关详细信息，请参阅我们的[HomeKit](~/ios/platform/homekit.md)指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是 iOS 8 中引入的一个框架，可为与运行状况相关的信息提供集中、协调和安全的数据存储。 操作系统可确保健康信息的隐私和安全性，以及针对用户的仪表板。 使用用户的权限，应用程序可以读取和写入各种健康信息。

有关在 Xamarin iOS 应用中使用此信息的详细信息，请参阅[HealthKit 指南简介](~/ios/platform/healthkit.md)。

## <a name="extending-iphone-functionality"></a>扩展 iPhone 功能
借助 iOS8，开发人员可更好地控制谁可以使用其应用，并增强了在第三方应用之间进行更开放式通信的能力。 应用扩展和文档选取器等功能可让你在 Apple 生态系统中使用应用程序的可能性。

### <a name="app-extensions"></a>应用扩展
应用扩展到过分简化其中是第三方应用彼此通信的方式。 若要保持高安全性标准并保持沙盒应用的完整性，则不会直接在应用程序之间进行此通信。 相反，它由中间的*扩展*执行。

创建应用扩展的第一步是定义正确的扩展点，这对于确保正确 Api 的行为和可用性非常重要。 若要在 Visual Studio for Mac 中创建应用扩展，请将新项目添加到解决方案中，将其添加到现有应用程序。

在 "**新建项目**" 对话框中**C#** ，导航到 > **iOS** > **Unified API** > **扩展**，如以下屏幕截图中所示：

![](introduction-to-ios8-images/image2.png "Creating a new extension")

"新建项目" 对话框提供了7个用于创建应用扩展的新项目模板，如下所述。 请注意，许多扩展与 iOS 中的其他新 Api 有关，如文档选取器：

- **操作**-这允许开发人员创建独特的自定义操作按钮，以允许用户执行某些任务
- **自定义键盘**–这允许开发人员通过添加自己的自定义键盘来向内置 Apple 键盘的范围添加。 使用常用键盘，Swype 可将其键盘带到 iOS。
- **文档选取器**–此对话框包含一个文档选取器视图控制器，该控制器允许用户访问应用程序沙箱之外的文件。
- **文档选取器文件提供程序**–这为使用文档选取器的文件提供安全存储。
- **照片编辑**–这会扩展照片应用程序中 Apple 提供的筛选器和编辑工具，使用户能够在编辑其照片时获得更多控制和更多选项。
- **今天**–这使应用程序能够在通知中心的 "今日" 部分显示小组件。

有关在 Xamarin 中使用应用扩展的详细信息，请参阅[应用扩展简介](~/ios/platform/extensions.md)指南。

### <a name="touch-id"></a>Touch ID

在 iOS 7 中引入 Touch ID 是对用户进行身份验证的一种方式，类似于密码。 但仅限于使用应用商店解锁设备、使用 iTunes，并仅对 iCloud 密钥链进行身份验证 

现在有两种方法可以使用触控 ID 作为 iOS 8 应用程序中使用本地身份验证 API 的身份验证机制。 目前不能使用本地身份验证来进行远程身份验证。

首先，它通过使用新的密钥链访问控制列表（Acl）来帮助现有的密钥链服务。 用户指纹的身份验证成功后，可以使用密钥链数据解除锁定。

其次，Localauthentication.framework 提供了两种方法来对应用程序进行本地身份验证。 开发人员应使用 `CanEvaluatePolicy` 来确定设备是否能够接受 Touch ID，然后 `EvaluatePolicy` 启动身份验证操作。

有关 Touch ID 并了解如何将其集成到 Xamarin iOS 应用程序的详细信息，请参阅[xamarin 指南中的 TOUCH id 和人脸 id](~/ios/platform/touch-id-face-id.md) 。

### <a name="document-picker"></a>文档选取器

文档选取器适用于用户 iCloud 驱动器，允许用户打开在其他应用中创建的文件，导入和操作这些文件，然后再次将其导出。 这会为用户创建直观的工作流，并因此获得更好的体验。 iCloud 同步更进一步，在一个应用程序中所做的任何更改也将在所有设备中一致地反映出来。

若要深入了解文档选取器并了解如何将其集成到 Xamarin iOS 应用程序中，请参阅[文档选取器指南简介](~/ios/platform/document-picker.md)。

### <a name="handoff"></a>Handoff

移交是更大的连续性功能的一部分，更进一步地集成 OS X 和 iOS。 这包括跨平台的 AirDrop、在 iPad 和 Mac 上执行 iPhone 调用的能力、iPad 和 Mac 上的短信，以及 iPhone 提供的 tethering 改进功能。

移交适用于 iOS 8 和 Yosemite，并且需要一个 iCloud 帐户登录到你想要使用的所有不同设备。 它应该适用于大多数预安装的 Apple 应用，包括 Safari、iWork、地图、日历和联系人。

有关详细信息，请参阅我们的[移交](~/ios/platform/handoff.md)指南。

## <a name="unified-storyboards"></a>统一的情节提要
iOS 8 包含用于创建用户界面（统一情节提要）的一种新易用的机制。 使用单个情节提要来涵盖所有不同的硬件屏幕大小，可以在真正的 "设计一次，使用多个" 样式创建快速、响应性视图。

在 iOS8 之前，开发人员使用 `UIInterfaceOrientation` 来区分纵向模式和横向模式，并 `UIInterfaceIdiom` 来区分 iOS 设备。 在 iOS8 中，不再需要为 iPhone 和 iPad 设备创建单独的情节提要，方向和设备是使用*大小类*确定的。

每个设备都是由大小类在垂直轴和水平轴中定义的，在 iOS 8 中有两种类型的大小类：

- **常规**-这适用于大屏幕尺寸（如 iPad）或小尺寸（如 UIScrollView）的小工具。
- **Compact** -这适用于较小的设备（如 iPhone）。 此大小会考虑设备的方向。

如果将两个概念一起使用，则结果为 2 x 2 网格，该网格定义可在不同方向上使用的不同可能大小，如下图所示：

![](introduction-to-ios8-images/image3.png "A diagram representing the 2 x 2 grid that defines the different possible sizes that can be used in both the differing orientations")

有关大小类的详细信息，请参阅[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)。

## <a name="photo-kit"></a>照片工具包
照片工具包是一个新的框架，它允许应用程序查询系统映像库并创建自定义用户界面来查看和修改其内容。 它包括多种类，这些类表示图像和视频资产，以及资产（如影集和文件夹）的集合。

有关详细信息，请参阅我们的[PhotoKit](~/ios/platform/photokit.md)指南。

## <a name="games"></a>游戏

<a name="scenekit" />

### <a name="scene-kit"></a>场景工具包

场景工具包是一个3D 场景图形 API，可简化使用三维图形的操作。 它是在 OS X 10.8 中首次引入的，现已推出 iOS 8。 利用场景工具包创建沉浸式三维可视化效果和休闲三维游戏，不需要在 OpenGL 中提供专业知识。 场景工具包是在常见场景图概念上构建的，它可以简化 OpenGL 和 OpenGL ES 的复杂性，使你可以轻松地将3D 内容添加到应用程序。 但是，如果您是 OpenGL 专家，则场景工具包也有很大的支持直接与 OpenGL 结合使用。 它还包括许多补充3D 图形的功能，如物理学，并与多个其他 Apple 框架（如核心动画、核心图像和 Sprite 工具包）紧密集成。

有关详细信息，请参阅我们的[SceneKit](~/ios/platform/gaming/scenekit.md)文档。

<a name="spritekit" />

### <a name="sprite-kit"></a>动画工具包

与 Apple 的2D 游戏框架一样，动画工具箱在 iOS 8 和 OS X Yosemite 中提供了一些有趣的新功能。 其中包括与场景工具包、着色器支持、照明、阴影、约束、正常地图生成和物理增强功能的集成。 特别是，新的物理学功能使向游戏添加逼真效果变得非常简单。

有关详细信息，请参阅我们的[SpriteKit](~/ios/platform/gaming/spritekit.md)文档。

## <a name="other-changes"></a>其他更改
除了上面所述的 iOS 8 中的主要更改，Apple 还更新了多个现有框架。 下面详细介绍了这些内容：

- **[核心映像](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – Apple 通过添加对矩形区域检测的更好支持以及图像内的 QR 码，扩展了其图像处理框架。 Mike Bluestein 在他的博客文章中探讨了如何[在 iOS 8 中进行图像检测](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>弃用的 API
在 iOS 8 中进行了所有改进后，许多 Api 已弃用。 下面详细介绍了其中一些信息。

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** -已弃用用于注册远程通知的方法和属性。 它们分别是 registerForRemoteNotificationTypes 和 enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** -特性和大小类已替换用于描述接口方向的方法和属性。 请参阅[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)，了解有关如何使用这些内容的详细信息。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** –此项已由 UISearchController 在 iOS8 中替换。

## <a name="summary"></a>总结
本文介绍了 iOS 8 中 Apple 引入的一些新功能。

## <a name="related-links"></a>相关链接

- [UIKitEnhancements （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [应用扩展简介](~/ios/platform/extensions.md)
- [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)
- [文档选取器简介](~/ios/platform/document-picker.md)
- [HealthKit 简介](~/ios/platform/healthkit.md)
- [手动照相机控件简介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [具有 Xamarin 的 Touch ID 和人脸 ID](~/ios/platform/touch-id-face-id.md)
- [统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)
