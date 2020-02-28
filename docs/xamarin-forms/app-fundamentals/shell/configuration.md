---
title: Xamarin.Forms Shell 页面配置
description: Shell 类定义了可用于在 Xamarin.Forms Shell 应用程序中配置页面外观的附加属性。 这包括设置页面颜色、禁用导航栏、禁用选项卡栏以及在导航栏中显示视图。
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
ms.openlocfilehash: dfa452addd7cfb838091afdfb350484998d0cc9d
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636082"
---
# <a name="xamarinforms-shell-page-configuration"></a>Xamarin.Forms Shell 页面配置

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

`Shell` 类定义了可用于在 Xamarin.Forms Shell 应用程序中配置页面外观的附加属性。 这包括设置页面颜色、设置页面演示模式、禁用导航栏、禁用选项卡栏以及在导航栏中显示视图。

## <a name="set-page-colors"></a>设置页面颜色

`Shell` 类定义了可用于在 Shell 应用程序中设置页面颜色的以下附加属性：

- `BackgroundColor`，属于 `Color` 类型，用于定义 Shell chrome 的背景色。 不会在 Shell 内容后面填充颜色。
- `DisabledColor`，属于 `Color` 类型，用于定义要对处于禁用状态的文本和图标进行阴影处理的颜色。
- `ForegroundColor`，属于 `Color` 类型，用于定义要对文本和图标进行阴影处理的颜色。
- `TitleColor`，属于 `Color` 类型，用于定义当前页面标题的颜色。
- `UnselectedColor`，属于 `Color` 类型，用于定义 Shell chrome 中未选文本和图标的颜色。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，这些属性可以作为数据绑定的目标，并使用 XAML style 设置样式。 此外，还可以使用级联样式表 (CSS) 设置这些属性。 有关详细信息，请参阅 [Xamarin.Forms Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

> [!NOTE]
> 还有可启用要定义的选项卡颜色的属性。 有关详细信息，请参阅[选项卡外观](tabs.md#tab-appearance)。

以下 XAML 演示如何在子类 `Shell` 类中设置颜色属性：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

在此示例中，颜色值将应用于 Shell 应用程序中的所有页面，除非在页面级别重写。

由于颜色属性是附加属性，因此还可以在单个页面上设置这些属性，以便在该页面上设置颜色：

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

或者，可以使用 XAML 样式设置颜色属性：

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

有关 XAML 样式的详细信息，请参阅[使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

## <a name="set-page-presentation-mode"></a>设置页面演示模式

默认情况下，当使用 `GoToAsync` 方法转到页面时，会出现一个小的导航动画。 不过，此行为是可以更改的，具体方法是将 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上的附加属性 `Shell.PresentationMode` 设置为 `PresentationMode` 枚举成员之一：

- `NotAnimated` 指明页面将在显示时不播放导航动画。
- `Animated` 指明页面将在显示时播放导航动画。 这是附加属性 `Shell.PresentationMode` 的默认值。
- `Modal` 指明页面将显示为模式页面。
- `ModalAnimated` 指明页面将显示为模式页面，并播放导航动画。
- `ModalNotAnimated` 指明页面将显示为模式页面，但不播放导航动画。

> [!IMPORTANT]
> `PresentationMode` 类型是 flags 枚举。 也就是说，可以在代码中应用枚举成员的组合。 不过，为了方便在 XAML 中使用，`ModalAnimated` 成员是 `Animated` 和 `Modal` 成员的组合，`ModalNotAnimated` 成员是 `NotAnimated` 和 `Modal` 成员的组合。 若要详细了解 flags 枚举，请参阅[作为位标志的枚举类型](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags)。

下面的 XAML 示例设置 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上的附加属性 `Shell.PresentationMode`：

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

在此示例中，[`ContentPage`](xref:Xamarin.Forms.ContentPage) 设置为在使用 `GoToAsync` 方法转到页面时显示为模式页面。

## <a name="enable-navigation-bar-shadow"></a>启用导航栏阴影

`Shell` 类型定义 `bool` 类型的 `NavBarHasShadow` 附加属性，用于控制导航栏是否有阴影。 默认情况下，属性的值为 `false`。

虽然可以在子类 `Shell` 对象上设置此属性，但也可以在想要启用导航栏阴影的任何页面上设置。 例如，以下 XAML 演示如何从 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 启用导航栏阴影：

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

这导致启用导航栏阴影。

## <a name="disable-the-navigation-bar"></a>禁用导航栏

`Shell` 类型定义 `bool` 类型的 `NavBarIsVisible` 附加属性，用于控制导航栏在显示页面时是否可见。 默认情况下，属性的值为 `true`。

虽然可以在子类 `Shell` 对象上设置此属性，但通常在想要使导航栏不可见的任何页面上设置。 例如，以下 XAML 演示如何从 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 禁用导航栏：

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

这会导致导航栏在显示页面时变得不可见：

![iOS 和 Android 上具有不可见导航栏的 Shell 页的屏幕截图](configuration-images/navigationbar-invisible.png "具有不可见导航栏的 Shell 页")

## <a name="disable-the-tab-bar"></a>禁用选项卡栏

`Shell` 类型定义 `bool` 类型的 `TabBarIsVisible` 附加属性，用于控制选项卡栏在显示页面时是否可见。 默认情况下，属性的值为 `true`。

虽然可以在子类 `Shell` 对象上设置此属性，但通常在想要使选项卡栏不可见的任何页面上设置。 例如，以下 XAML 演示如何从 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 禁用选项卡栏：

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

这会导致选项卡栏在显示页面时变得不可见：

![iOS 和 Android 上具有不可见选项卡栏的 Shell 页的屏幕截图](configuration-images/tabbar-invisible.png "具有不可见选项卡栏的 Shell 页")

## <a name="display-views-in-the-navigation-bar"></a>在导航栏中显示视图

`Shell` 类定义 `View` 类型的 `TitleView` 附加属性，可用于在导航栏中显示任何 Xamarin.Forms [`View`](xref:Xamarin.Forms.View)。

虽然可以在子类 `Shell` 对象上设置此属性，但也可以在想要在导航栏中显示视图的任何页面上设置。 例如，以下 XAML 演示如何在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的导航栏中显示 [`Image`](xref:Xamarin.Forms.Image)：

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

这导致图像显示在页面的导航栏中：

![iOS 和 Android 上具有标题视图的 Shell 页的屏幕截图](configuration-images/titleview.png "具有标题视图的 Shell 页")

> [!IMPORTANT]
> 如果导航栏已变得不可见，则使用 `NavBarIsVisible` 附加属性不会显示标题视图。

很多视图不会出现在导航栏中，除非使用 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性指定视图的大小，或使用 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性指定视图的位置。

因为 [`Layout`](xref:Xamarin.Forms.Layout) 类派生自 [`View`](xref:Xamarin.Forms.View) 类，所以可以设置 `TitleView` 附加属性来显示包含多个视图的布局类。 同样，因为 [`ContentView`](xref:Xamarin.Forms.ContentView) 类派生自 [`View`](xref:Xamarin.Forms.View) 类，所以可以设置 `TitleView` 附加属性来显示包含单个视图的 `ContentView`。

## <a name="page-visibility"></a>页面可见性

Shell 涉及页面可见性（通过 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 属性设置）。 因此，当页面的 `IsVisible` 属性设置为 `false` 时，它在 Shell 应用中不可见，因此也就不能转到它。

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
