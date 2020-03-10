---
title: 系统要求
description: 本文档列出了在 Mac 和 Windows 计算机上使用 Xamarin 生成应用的系统要求。 它还链接到安装说明。
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: davidortinau
ms.author: daortin
ms.date: 10/16/2019
ms.openlocfilehash: c4eaebc8d5f184b1108815c37e992fa97e67aa8f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914199"
---
# <a name="system-requirements"></a>系统要求

Xamarin 产品依赖于 Apple 和 Google 中的平台 SDK 才能面向 iOS 或 Android，因此系统要求应与这些平台的要求匹配。 此页列出了 Xamarin 平台的系统兼容性以及建议的开发环境和 SDK 版本。

有关获取软件和所需 SDK 的详细信息，请参阅[安装说明](#installation-instructions)。

## <a name="development-environments"></a>开发环境

此表显示了可使用不同开发工具和操作系统组合生成的平台：

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> 若要在 Windows 计算机上针对 iOS 进行开发，必须有[可通过网络访问的 Mac 计算机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，以供远程编译和调试。 这同样适用于 Visual Studio 在 Mac 计算机上的 Windows VM 内运行的情况。

## <a name="macos-requirements"></a>macOS 要求

使用 Mac 计算机进行 Xamarin 开发需要以下软件/SDK 版本。 检查操作系统版本，并按照 [Xamarin 安装程序](#installation-instructions)相关说明操作。

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> 可通过 [developer.apple.com](https://developer.apple.com/xcode/download/) 或 Mac App Store 安装（和更新）Xcode。

### <a name="testing--debugging-on-macos"></a>macOS 上的测试和调试

- 可通过 USB 将 Xamarin 移动应用程序部署到物理设备以进行测试和调试（Apple Watch 应用首先部署到配对的 iPhone）。
- 可直接在开发计算机上测试 Xamarin.Mac 应用。

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> Xamarin.Mac 4.8 仅支持 macOS 10.9 (Mavericks) 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="windows-requirements"></a>Windows 要求

使用 Windows 计算机进行 Xamarin 开发需要以下软件/SDK 版本。
检查操作系统版本（请确保使用的不是 Express  版本的 Visual Studio - 如果使用的是该版本，则请考虑更新至 Community  版本）。
Visual Studio 2019 和 Visual Studio 2017 安装程序支持自动安装 Xamarin（使用 .NET 工作负载的移动开发）  。

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
>
> - 适用于 Visual Studio 的 Xamarin 支持 Visual Studio 2019 或 Visual Studio 2017 版本（Community、Professional 和 Enterprise）。
> - 若要使用最新的 Android 和 iOS SDK，需使用最新版本的 Visual Studio。 如需了解特定版本要求，请参阅 [Xamarin.Android 发行说明](/xamarin/android/release-notes/)和 [Xamarin.iOS 发行说明](/xamarin/ios/release-notes/)。
> - 必须在 Windows 10 上安装 Visual Studio 2017，才能为通用 Windows 平台 (UWP) 开发 Xamarin.Forms 应用。 建议使用 Visual Studio 2019。

### <a name="testing--debugging-on-windows"></a>Windows 上的测试和调试

可通过 USB 或无线将 Xamarin 移动应用程序部署到物理设备以进行测试和调试（iOS 设备必须连接到 Mac 计算机，而不是运行 Visual Studio 的计算机）。

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>安装说明

可以随 [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation) 下载适用于 macOS 的最新版 Xamarin。 对于 Windows，请按照 [Visual Studio 安装说明](https://docs.microsoft.com/visualstudio/install/install-visual-studio)进行安装。

[新增功能页面](~/whats-new/index.yml)上提供了当前产品版本的完整列表。 此页还链接到发行说明。

有关每个平台的特定[安装](~/get-started/installation/index.md)说明，请访问：

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

还提供有关 [Xamarin.Forms 支持的平台](~/get-started/supported-platforms.md)的其他信息。

## <a name="related-links"></a>相关链接

- [下载 Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Xamarin.Forms 发行说明](/xamarin/xamarin-forms/release-notes/)
- [Xamarin.Android 发行说明](/xamarin/android/release-notes/)
- [Xamarin.iOS 发行说明](/xamarin/ios/release-notes/)
