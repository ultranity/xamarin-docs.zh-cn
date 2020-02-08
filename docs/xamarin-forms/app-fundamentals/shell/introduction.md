---
title: Xamarin.Forms Shell 简介
description: Xamarin.Forms Shell 提供了大多数应用程序所需的基本功能，包括常见的导航用户体验、基于 URI 的导航方案，以及集成的搜索处理程序。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cb2ae3afe9db86d4db603d499ef0e75e7cbbf552
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940404"
---
# <a name="xamarinforms-shell-introduction"></a>Xamarin.Forms Shell 简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数移动应用程序所需的基本功能，包括：

- 用于描述应用程序的视觉层次结构的单个位置。
- 常见的导航用户体验。
- 允许导航到应用程序中的任何页面的基于 URI 的导航方案。
- 集成的搜索处理程序。

此外，Shell 应用程序还因提高了呈现速度和减少了内存消耗而受益。

> [!IMPORTANT]
> 现有的应用程序可以采用 Shell，立即获益于导航、性能和扩展性方面的改进。

## <a name="platform-support"></a>平台支持

Xamarin.Forms Shell 在 iOS 和 Android 上完全可用，但在通用 Windows 平台 (UWP) 上仅部分可用。 此外，Shell 当前在 UWP 上处于实验性阶段，只有在调用 `Forms.Init` 之前，将以下代码行添加到 UWP 项目的 `App` 类后才可使用：

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

有关如何向 Xamarin.Forms 解决方案添加 UWP 项目的信息，请参阅[设置 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)。

## <a name="shell-navigation-experience"></a>Shell 导航体验

Shell 提供了基于浮出控件和选项卡的固定导航体验。 Shell 应用程序中的顶级导航是浮出控件或底部选项卡栏，具体取决于应用程序的导航要求。 下面的示例演示了顶级导航是浮出控件的应用程序：

[![iOS 和 Android 上的 Shell 浮出控件的屏幕截图](introduction-images/flyout.png "Shell 浮出控件")](introduction-images/flyout-large.png#lightbox "Shell 浮出控件")

选择浮出控件项导致选中并显示表示项的底部选项卡：

[![iOS 和 Android 上的 Shell 底部选项卡的屏幕截图](introduction-images/monkeys.png "Shell 底部选项卡")](introduction-images/monkeys-large.png#lightbox "Shell 底部选项卡")

> [!NOTE]
> 浮出控件未打开时，系统可以将底部选项卡栏视作应用程序中的顶级导航。

每个选项卡都会显示 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 但是，如果底部选项卡包含多个页面，则页面可通过顶部选项卡栏进行导航：

[![iOS 和 Android 上的 Shell 顶部选项卡的屏幕截图](introduction-images/cats.png "Shell 顶部选项卡")](introduction-images/cats-large.png#lightbox "Shell 顶部选项卡")

在每个选项卡中，可以导航到其他 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象：

[![iOS 和 Android 上的 Shell 页面导航的屏幕截图](introduction-images/cat-details.png "Shell 应用导航")](introduction-images/cat-details-large.png#lightbox "Shell 应用导航")

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
