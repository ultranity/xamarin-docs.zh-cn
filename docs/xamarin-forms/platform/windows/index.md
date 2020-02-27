---
title: Windows 平台功能
description: 本文介绍了 Xamarin 中提供的 Windows 平台支持。
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 694ec24697937b114036eceab1cafd5aae3617d8
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635547"
---
# <a name="windows-platform-features"></a>Windows 平台功能

为 Windows 平台开发 Xamarin. Forms 应用程序需要 Visual Studio。 "[支持的平台" 页](~/get-started/supported-platforms.md)包含有关先决条件的详细信息。

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>平台特定内容

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

以下特定于平台的功能是为 Xamarin 提供的 "窗体视图"、"页面" 和 "布局" 通用 Windows 平台（UWP）：

- 为[`VisualElement`](xref:Xamarin.Forms.VisualElement)设置访问密钥。 有关详细信息，请参阅[Windows 上的 VisualElement 访问密钥](visualelement-access-keys.md)。
- 禁用受支持[`VisualElement`](xref:Xamarin.Forms.VisualElement)上的旧版颜色模式。 有关详细信息，请参阅[VisualElement 旧版 Color Mode On Windows](legacy-color-mode.md)。

以下特定于平台的功能适用于 UWP 上的 Xamarin 窗体视图：

- 检测[`Entry`](xref:Xamarin.Forms.Entry)、 [`Editor`](xref:Xamarin.Forms.Editor)和[`Label`](xref:Xamarin.Forms.Label)实例中的文本内容的读取顺序。 有关详细信息，请参阅[Windows 上的 InputView 读取顺序](inputview-reading-order.md)。
- 在[`ListView`](xref:Xamarin.Forms.ListView)中启用点击手势支持。 有关详细信息，请参阅[ListView SelectionMode On Windows](listview-selectionmode.md)。
- 启用要更改的 `RefreshView` 的请求方向。 有关详细信息，请参阅[Windows 上的 RefreshView 拉取方向](refreshview-pulldirection.md)。
- 使[`SearchBar`](xref:Xamarin.Forms.SearchBar)能够与拼写检查引擎进行交互。 有关详细信息，请参阅[Windows 上的 SearchBar 拼写检查](searchbar-spell-check.md)。
- 启用[`WebView`](xref:Xamarin.Forms.WebView)以显示 UWP 消息对话框中的 JavaScript 警报。 有关详细信息，请参阅[Windows 上的 Web 视图 JavaScript 警报](webview-javascript-alert.md)。

以下特定于平台的功能适用于 UWP 上的 Xamarin 窗体页：

- 折叠[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)导航栏。 有关详细信息，请参阅[Windows 上的 MasterDetailPage 导航栏](masterdetailpage-navigation-bar.md)。
- 设置工具栏的放置选项。 有关详细信息，请参阅[在 Windows 上放置页面工具栏](page-toolbar-placement.md)。
- 允许在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)工具栏上显示页面图标。 有关详细信息，请参阅 [Windows 上的 TabbedPage 图标](tabbedpage-icons.md)。

以下特定于平台的功能适用于 UWP 上的 Xamarin [`Application`](xref:Xamarin.Forms.Application)类：

- 指定将从其加载图像资产的项目中的目录。 有关详细信息，请参阅[Windows 上的默认图像目录](default-image-directory.md)。

## <a name="platform-support"></a>平台支持

Visual Studio 中提供的 Xamarin 模板包含一个通用 Windows 平台（UWP）项目。

> [!NOTE]
> Xamarin; Forms 1.x 和2.x 支持_Windows Phone 8 个 Silverlight_、 _Windows Phone 8.1_和_Windows 8.1_应用程序开发。 但是，这些项目类型已弃用。

## <a name="getting-started"></a>入门

转到文件 > Visual Studio 中的**新 > 项目**，然后选择一个**跨平台 > 空白应用（Xamarin）** 模板以开始。

旧的 Xamarin. Forms 解决方案或在 macOS 上创建的解决方案将不会具有上面列出的所有 Windows 项目（但需要手动添加）。 如果你想要作为目标的 Windows 平台尚未处于解决方案中，请访问[设置说明](installation/index.md)以添加所需的 windows 项目类型。

## <a name="samples"></a>示例

Charles Petzold 的[所有示例都](https://github.com/xamarin/xamarin-forms-book-preview-2)是[*用 Xamarin 创建移动应用，其中*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包括通用 Windows 平台（适用于 Windows 10）项目。

["Scott Hanselman" 演示应用](https://github.com/jamesmontemagno/Hanselman.Forms)单独提供，还包括 Apple Watch 和 android 磨损项目（分别使用 Xamarin 和 xamarin），而 xamarin 则不会在这些平台上运行。

## <a name="related-links"></a>相关链接

- [设置 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)
