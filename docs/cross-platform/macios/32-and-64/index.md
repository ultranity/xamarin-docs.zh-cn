---
title: 32/64 位平台注意事项
description: 本文档介绍面向 Xamarin 或 Xamarin 应用程序的32位和64位体系结构时需要注意的各种注意事项。
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 5ba451de857444bc5b12b750ae479b62abdb75a3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910802"
---
# <a name="3264-bit-platform-considerations"></a>32/64 位平台注意事项

尽管 iOS 和 macOS 一直支持32和64位应用，但 Apple 已逐渐弃用32位支持。

从 iOS 11 开始，32位应用将不再启动，[应用商店的所有提交都必须支持64位](https://developer.apple.com/news/?id=06282017b)。

从2018年1月开始，[提交到 Mac App Store 的新应用必须支持64位](https://developer.apple.com/news/?id=06282017a)，并且现有应用必须由6月2018更新。

Xamarin 的 Classic API （`XamMac.dll` 和 `monotouch.dll`）仅支持32位应用程序。 但是，新的 Xamarin 和 Xamarin 应用程序默认使用[Unified API](~/cross-platform/macios/unified/index.md) （`Xamarin.iOS` 和 `Xamarin.Mac`），因此，在必要时可以同时针对32和64位。

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 Xamarin iOS 应用的64位版本

> [!WARNING]
> 出于历史原因，此部分包括在内，有助于将较旧的 Xamarin iOS 项目移到 Unified API 并支持64位。 默认情况下，所有新的 Xamarin iOS 项目都将使用 Unified API 和目标64位。

对于已转换为 Unified API 的 Xamarin iOS 移动应用程序，开发人员必须手动将生成设置更新为面向64位：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在**Solution Pad**中，双击应用程序的项目，打开 "**项目选项**" 窗口。
2. 选择 " **IOS 生成**"。
3. 对于 iPhone 模拟器，请在 "**支持的体系结构**" 下拉列表中，选择 " **x86\_64** " 或 " **i386 + x86\_64**"：

   [![将支持的体系结构设置为 x86\_64 或 i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. 对于物理设备，请选择可用的**ARM64**组合之一：

   [![将支持的体系结构设置为 ARM64 组合之一](Images/Image02.png "将支持的体系结构设置为 ARM64 组合之一")](Images/Image02-large.png#lightbox)

5. 单击“确定”。
6. 执行全新生成。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在**解决方案资源管理器**中，右键单击应用的项目，然后选择 "**属性**"。
2. 选择 " **IOS 生成**"。
3. 对于 iPhone 模拟器，请将**支持的体系结构**设置为**x86\_64**或**i386 + x86\_64**： 

   [![将支持的体系结构设置为 x86_64 或 i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. 对于物理设备，请选择可用的**ARM64**组合之一：
    
   [![将支持的体系结构设置为 ARM64 组合之一](Images/VS01.png "将支持的体系结构设置为 ARM64 组合之一")](Images/VS01-large.png#lightbox)

5. 保存所做更改。
6. 执行全新生成。

-----

ARMv7s 仅支持 iPhone 5 （或更高版本）中包含的 A6 处理器。 ARMv7 代码比 ARMv6 更快且更小，仅适用于 iPhone 3GS 和更高版本，并且在面向 iPad 或最低 iOS 版本5.0 时，Apple 需要此代码。 ARMv6 适用于所有设备，但在 Xcode 4.5 和更高版本随附的编译器中不再支持该功能。 

需要 ARM64 来支持 iPhone 6 或其他64位设备上的 iOS 8，并将在 iTunes 应用商店中提交新的或更新现有应用程序时 Apple 要求使用。

若要全面了解各种 iOS 设备的功能，请查看 Apple 的[设备兼容性](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html)文档。

### <a name="64-bit-and-binary-size-increases"></a>64位和二进制大小增加

在 Apple 从32位转换到64时，iOS 应用需要在32位和64位硬件上运行。 因此，Xamarin 的 Unified API 允许开发人员以这两种方法为目标。

面向32位和64位体系结构的目标会显著增加应用程序的大小。 不过，这样做会允许较新的设备运行优化的代码，同时仍支持较旧的设备。

> [!IMPORTANT]
> 如果在向 iTunes 应用商店提交 iOS 应用程序时收到以下消息，则为 _"警告 ITMS-9000：缺少64位支持。从2015年2月1日开始，已上传到应用商店的新 iOS 应用必须包括64位支持，并通过 iOS 8 SDK （包括在 Xcode 6 或更高版本中）构建。若要在项目中启用64位，建议使用默认的 Xcode 生成设置 "标准体系结构" 以使用32位和64位代码生成单个二进制文件。 "_ 需要将支持的体系结构切换为可用的**ARM64**组合之一（如上所示），重新编译并重新提交。

## <a name="mac"></a>Mac

> [!IMPORTANT]
> 从2018年1月开始，提交到 Mac App Store 的所有新 Mac 应用都必须支持64位。 现有的 Mac 应用商店应用及其更新必须支持64位（从6月 6 2018 日开始）。 请参阅[Apple 的公告](https://developer.apple.com/news/?id=06282017a)和[指南，其中介绍了如何将你的 Xamarin 应用程序更新到64位](~/cross-platform/macios/32-and-64/mac-64-bit.md)。

大多数新式 Mac 计算机都支持32位和64位的应用程序。   MacOS 10.6 （雪 Leopard）是在32位系统上运行的最后一个操作系统。   自2010以来发布的大多数 Mac 都支持这两个系统。

与 iOS 不同，在最新版本的 macOS 中引入的许多新框架仅在64位模式下受支持（CloudKit、EventKit、GameController、Localauthentication.framework、MediaLibrary、MultipeerConnectivity、NotificationCenter、GLKit、SpriteKit、社交、以及其他 MapKit）。

Unified API 允许开发人员选择他们想要生成的应用程序类型：32位或64位。

**32 位应用程序**将在32位和64位 Mac 计算机上运行，其地址空间限制为32位，并要求所有库都为32位。

如果你有32位依赖64项（如果你想要下载较小的下载，或者如果迁移到64位，则没有性能优势），则通常会使用此模式。

此模式受到限制，因为你将无法使用 macOS Mavericks 和 macOS Yosemite 中提供的多个框架。

**64 位应用程序**将仅在64位 Mac 设备上运行。

对于 Mac，这是首选的操作模式，因为目前使用的大多数 Mac 支持64位模式，并且你有权访问 Apple 提供的完整框架集。

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>启用 Xamarin Mac 应用的64位版本

有关使用 Xamarin 生成64位应用的信息，请参阅将[Xamarin 统一应用程序更新到64位](~/cross-platform/macios/32-and-64/mac-64-bit.md)指南。

## <a name="related-links"></a>相关链接

- [经典与 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
