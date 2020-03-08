---
title: Xamarin.Forms 支持的平台
description: Xamarin.Forms 平台和开发系统要求。
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2020
ms.openlocfilehash: 5751983bf4a818e8ee91af98b0c05658efaca8b2
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915656"
---
# <a name="xamarinforms-supported-platforms"></a>Xamarin.Forms 支持的平台

可对以下操作系统编写 Xamarin.Forms 应用程序：

- iOS 9 或更高版本。
- Android 4.4 (API 19) 或更高版本（[详细信息](#android-platform-support)）。 但建议使用 Android 5.0 (API 21) 作为最小 API。 这可确保与所有 Android 支持库完全兼容，同时仍面向大多数 Android 设备。
- Windows 10 通用 Windows 平台。

可以在 Visual Studio 中生成适用于 iOS、Android 和通用 Windows 平台 (UWP) 的 Xamarin.Forms 应用。 但是，使用最新版本的 Xcode 和 Apple 指定的最低版本 macOS 进行 iOS 开发需要使用联网 Mac。 有关详细信息，请参阅 [Windows 要求](~/cross-platform/get-started/requirements.md#windows-requirements)。

可以在 Visual Studio for Mac 中生成适用于 iOS 和 Android 的 Xamarin.Forms 应用。 有关详细信息，请参阅 [macOS 要求](~/cross-platform/get-started/requirements.md#macos-requirements)。

> [!NOTE]
> 使用 Xamarin.Forms 开发应用需要熟悉 [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="additional-platform-support"></a>其他平台支持

Xamarin.Forms 支持 iOS、Android 和 Windows 以外的其他平台：

- Samsung Tizen
- macOS
- GTK#
- WPF

这些平台状态可在 [Xamarin.Forms GitHub 平台支持 wiki](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support) 上找到。

## <a name="android-platform-support"></a>Android 平台支持

应安装最新的 Android SDK 工具和 Android API 平台。 可使用 [Android SDK 管理器](~/android/get-started/installation/android-sdk.md)更新到最新版本。

此外，Android 项目的目标/编译版本必须设置为“使用最新安装的平台”。 但是，最低版本可设置为 API 19，因此可继续支持使用 Android 4.4（以及更高版本）的设备。 在“项目选项”中可设置这些值：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

“项目选项”>“应用程序”>“应用程序属性”

![Visual Studio 中的 Android 生成选项](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

“生成”>“常规”

![选择最新目标框架](requirements-images/options-general-sml.png)

“生成”>“Android 应用程序”

![为应用选择最低和目标 Android 版本](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>已弃用的平台

使用 Xamarin.Forms 3.0 或更高版本时，不支持这些平台：

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
