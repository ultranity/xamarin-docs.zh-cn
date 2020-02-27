---
title: Xamarin 中的 iOS 平台功能
description: 向 Xamarin 应用程序添加 iOS 特定功能。
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 015db40ce983d979109d4cce32c011f8c61a729c
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635659"
---
# <a name="ios-platform-features-in-xamarinforms"></a>Xamarin 中的 iOS 平台功能

开发适用于 iOS 的 Xamarin 应用程序需要 Visual Studio。 "[支持的平台" 页](~/get-started/supported-platforms.md)包含有关先决条件的详细信息。

## <a name="platform-specifics"></a>平台特定内容

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

以下特定于平台的功能适用于 Xamarin。 Forms 视图、页面和 iOS 上的布局：

- 对任何[`VisualElement`](xref:Xamarin.Forms.VisualElement)的模糊支持。 有关详细信息，请参阅[iOS 上的 VisualElement 模糊](visualelement-blur.md)。
- 禁用受支持[`VisualElement`](xref:Xamarin.Forms.VisualElement)上的旧版颜色模式。 有关详细信息，请参阅[VisualElement 旧版彩色模式 On iOS](legacy-color-mode.md)。
- 启用[`VisualElement`](xref:Xamarin.Forms.VisualElement)上的投影。 有关详细信息，请参阅[iOS 上的 VisualElement 投影](visualelement-drop-shadow.md)。
- 使[`VisualElement`](xref:Xamarin.Forms.VisualElement)对象成为触控事件的第一个响应方。 有关详细信息，请参阅[VisualElement First 响应](visualelement-first-responder.md)者。

以下特定于平台的功能适用于适用于 iOS 的 Xamarin 视图：

- 设置[`Cell`](xref:Xamarin.Forms.Cell)背景色。 有关详细信息，请参阅[iOS 上的单元格背景色](cell-background-color.md)。
- 控制何时在[`DatePicker`](xref:Xamarin.Forms.DatePicker)中进行项目选择。 有关详细信息，请参阅[iOS 上的 DatePicker 项选择](datepicker-selection.md)。
- 通过调整字体大小确保输入文本适合[`Entry`](xref:Xamarin.Forms.Entry) 。 有关详细信息，请参阅[iOS 上的输入字体大小](entry-font-size.md)。
- 在[`Entry`](xref:Xamarin.Forms.Entry)中设置光标颜色。 有关详细信息，请参阅[iOS 上的入口游标颜色](entry-cursor-color.md)。
- 控制是否[`ListView`](xref:Xamarin.Forms.ListView)标题单元在滚动过程中浮动。 有关详细信息，请参阅[iOS 上的 ListView 组标头样式](listview-group-header-style.md)。
- 控制在更新[`ListView`](xref:Xamarin.Forms.ListView)项集合时是否禁用行动画。 有关详细信息，请参阅[在 iOS 上 ListView 行动画](listview-row-animations.md)。
- 在[`ListView`](xref:Xamarin.Forms.ListView)上设置分隔符样式。 有关详细信息，请参阅[iOS 上的 ListView 分隔符样式](listview-separator-style.md)。
- 控制何时在[`Picker`](xref:Xamarin.Forms.Picker)中进行项目选择。 有关详细信息，请参阅[iOS 上的选取器项目选择](picker-selection.md)。
- 通过在[`Slider`](xref:Xamarin.Forms.Slider)栏上的某个位置上点击来设置[`Slider.Value`](xref:Xamarin.Forms.Slider.Value)属性，而不是通过拖动 `Slider` 拇指来设置。 有关详细信息，请参阅[滚动条上](slider-thumb.md)的滚动条。
- 控制在打开 `SwipeView`时使用的转换。 有关详细信息，请参阅[SwipeView 滑动过渡模式](swipeview-swipetransitionmode.md)。
- 控制何时在[`TimePicker`](xref:Xamarin.Forms.TimePicker)中进行项目选择。 有关详细信息，请参阅[iOS 上的 TimePicker 项选择](timepicker-selection.md)。

以下特定于平台的功能适用于 iOS 上的 Xamarin 窗体页：

- 隐藏[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)上的导航栏分隔符。 有关详细信息，请参阅[iOS 上的 NavigationPage Bar 分隔符](navigation-bar-separator.md)。
- 控制是否半透明的导航栏。 有关详细信息，请参阅[导航栏半透明度 On iOS](navigation-bar-translucent.md)。
- 控制是否调整[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)上的状态栏文本颜色以匹配导航栏的发光度。 有关详细信息，请参阅[NavigationPage Bar 文本 Color Mode On iOS](status-bar-text-color.md)。
- 控制是否将页标题显示为页导航栏中的大型标题。 有关详细信息，请参阅[iOS 上的大型页面标题](page-large-title.md)。
- 设置[`Page`](xref:Xamarin.Forms.Page)上主指示器的可见性。 有关详细信息，请参阅[iOS 上的主指示器可见性](page-home-indicator.md)。
- 在[`Page`](xref:Xamarin.Forms.Page)上设置状态栏可见性。 有关详细信息，请参阅[iOS 上的页面状态栏可见性](page-status-bar-visibility.md)。
- 确保该页面内容位于上是安全的所有 iOS 设备的屏幕区域。 有关详细信息，请参阅[iOS 上的安全区域布局指南](page-safe-area-layout.md)。
- 设置模式页的呈现样式。 有关详细信息，请参阅[模式页面表示形式样式](page-presentation-style.md)。
- 设置[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上的选项卡的半透明度模式。 有关详细信息，请参阅[TabbedPage 半透明选项卡栏 On iOS](tabbedpage-translucent-tabbar.md)。

以下特定于平台的功能适用于适用于 iOS 的 Xamarin 布局布局：

- 控制[`ScrollView`](xref:Xamarin.Forms.ScrollView)是否处理触摸手势或将其传递给其内容。 有关详细信息，请参阅[ScrollView 内容涉及 iOS](scrollview-content-touches.md)。

在 iOS 上，为 Xamarin [`Application`](xref:Xamarin.Forms.Application)类提供以下特定于平台的功能：

- 禁用命名字体大小的辅助功能缩放。 有关详细信息，请参阅[iOS 上命名字体大小的辅助功能缩放](named-font-size-scaling.md)。
- 启用控件的布局和呈现要在主线程上执行更新。 有关详细信息，请参阅[iOS 上的主线程控制更新](main-thread-updates-ui.md)。
- 启用滚动视图中的[`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer)可使用滚动视图捕获和共享平移手势。 有关详细信息，请参阅[iOS 上的并发平移手势识别](application-pan-gesture.md)。

## <a name="ios-specific-formatting"></a>特定于 iOS 的格式

Xamarin 可以设置跨平台用户界面样式和颜色，但也可以使用 iOS 项目中的平台 Api 来设置 iOS 主题。

[阅读](formatting.md)有关使用 IOS 特定 api 设置用户界面格式的详细信息，如**info.plist**配置和 `UIAppearance` API。

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>其他 iOS 功能

使用[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)和[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，可以将各种本机功能合并到适用于 iOS 的 Xamarin 应用程序中。
