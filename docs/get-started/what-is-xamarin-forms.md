---
title: 什么是 Xamarin.Forms？
description: 本文介绍 Xamarin.Forms 和相关库。
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: aaceb6089a5b7e5f0551dafe9ef1fe50d01433d9
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374018"
---
# <a name="what-is-xamarinforms"></a>什么是 Xamarin.Forms？

[![iOS 和 Android 中的示例 Xamarin.Forms 应用程序的屏幕截图](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Forms 是一个开放源代码 UI 框架。 Xamarin.Forms 使开发人员可以从单个共享基本代码生成 Android、iOS 和 Windows 应用程序。

Xamarin.Forms 使开发人员可以 C# 中通过代码隐藏在 XAML 中创建用户界面。 这些界面在每个平台上呈现为高性能本机控件。

## <a name="who-xamarinforms-is-for"></a>Xamarin.Forms 的适用对象

Xamarin.Forms 适用于具有以下目标的开发人员：

- 跨平台共享 UI 布局和设计。
- 跨平台共享代码、测试和业务逻辑。
- 使用 Visual Studio 在 C# 中编写跨平台应用。

## <a name="how-xamarinforms-works"></a>Xamarin.Forms 的工作原理

![Xamarin.Forms 体系结构关系图](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Forms 提供了一个一致的 API，用于跨平台创建 UI 元素。 此 API 可以在 XAML 或 C# 中实现，支持针对模型-视图-视图模型 (MVVM) 等模式的数据绑定。

在运行时，Xamarin.Forms 使用平台呈现器将跨平台 UI 元素转换为 Android、iOS 和 UWP 上的本机控件。 这使开发人员可以获得本机外观和性能，同时实现跨平台代码共享的优势。

Xamarin.Forms 应用程序通常由共享 .NET Standard 库和各个平台项目组成。 共享库包含 XAML 或 C# 视图以及任何业务逻辑（如服务、模型或其他代码）。 平台项目包含应用程序所需的任何特定于平台的逻辑或包。

Xamarin.Forms 使用 Xamarin 跨平台本机运行 .NET 应用程序。 有关 Xamarin 的详细信息，请参阅[什么是 Xamarin？](~/get-started/what-is-xamarin.md)。

## <a name="additional-tools"></a>其他工具

Xamarin.Forms 包含一个大型 NuGet 包生态系统，可向应用程序添加各种功能。 本部分介绍几个常用 NuGet 包。

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin.Essentials 是一个库，可为本机设备功能提供跨平台 API。 与 Xamarin 本身一样，Xamarin.Essentials 是一种简化本机实用工具访问过程的抽象。 Xamarin.Essentials 提供的实用工具的一些示例包括：

- 设备信息
- 文件系统
- 加速计
- 电话拨号程序
- 文本到语音转换
- 屏幕锁定

有关详细信息，请参阅 [Xamarin.Essentials](~/essentials/index.md)。

### <a name="shell"></a>shell

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数应用程序所需的基本功能。 Shell 提供的功能的一些示例包括：

- 常见导航体验
- 基于 URI 的导航方案
- 集成搜索处理程序

有关详细信息，请参阅 [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>平台特定内容

Xamarin.Forms 提供了一个公共 API，它可跨平台呈现本机控件，但特定平台的功能可能在其他平台上不存在。 例如，Android 平台具有用于在 `ListView` 中快速滚动的本机功能，但 iOS 却没有。 通过 Xamarin.Forms 平台特定内容可以使用仅在特定平台上提供的功能，而无需创建自定义呈现器或效果。

Xamarin.Forms 包括用于各种特定于平台的功能的预建解决方案。 有关详细信息，请参见:

- [Xamarin.Forms 平台特定内容](~/xamarin-forms/platform/platform-specifics/index.md)
- [Android 平台特定内容](~/xamarin-forms/platform/android/index.md)
- [iOS 平台特定内容](~/xamarin-forms/platform/ios/index.md)
- [Windows 平台特定内容](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>材料视觉对象

Xamarin.Forms 材料视觉对象用于将材料设计规则应用于 Xamarin.Forms 应用程序。 Xamarin.Forms 材料视觉对象利用视觉对象属性将自定义呈现器选择性地应用于 UI，从而使应用程序在 iOS 和 Android 中具有一致的外观。

有关详细信息，请参阅 [Xamarin.Forms 材料视觉对象](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 入门](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.Forms 材料视觉对象](~/xamarin-forms/user-interface/visual/material-visual.md)
