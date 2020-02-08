---
title: Xamarin.Forms 字符串格式
description: 本文介绍如何使用 Xamarin.FOrms 数据绑定设置对象格式并将对象显示为字符串。 通过将绑定的字符串格式设置为带有占位符的标准 .NET 格式设置字符串，可实现实现此操作。
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: bdd28e1ce6d36a0a025ac43a709af2e38a313526
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940382"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin.Forms 字符串格式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

有时，使用数据绑定来显示代表对象或值的字符串十分方便。 例如，可能想使用 `Label` 来显示 `Slider` 当前的值。 在此数据绑定中，`Slider` 是源，目标是 `Label` 的 `Text` 属性。

在代码中显示字符串时，最强大的工具是静态 [`String.Format`](xref:System.String.Format(System.String,System.Object)) 方法。 格式设置字符串包括特定于各种类型的对象的格式代码，也可以包括其他文本以及正在进行格式设置的值。 有关字符串格式的详细信息，请参阅[设置 .NET 中类型的格式](/dotnet/standard/base-types/formatting-types/)一文。

## <a name="the-stringformat-property"></a>StringFormat 属性

此功能将沿用到数据绑定中：将 `Binding` 的 [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) 属性（或 `Binding` 标记扩展的 [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) 属性）设置为带一个占位符的标准 .NET 格式设置字符串：

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

请注意，格式设置字符串由单引号（撇号）字符分隔，以帮助 XAML 分析器避免将大括号视为另一个 XAML 标记扩展。 否则，不带单引号字符的此字符串会与用于在 `String.Format` 的调用中显示浮点值的字符串相同。 `F2` 的格式设置规范导致值在显示时会带两个小数位数。

仅当目标属性为类型 `string` 且绑定模式为 `OneWay` 或 `TwoWay` 时，`StringFormat` 属性才有意义。 对于双向绑定，`StringFormat` 仅适用于从源向目标传递的值。

将在[绑定路径](binding-path.md)中的下一篇文章中看到，数据绑定可能会变得相当复杂且费解。 调试这些数据绑定时，可以将 `Label` 添加到带 `StringFormat` 的 XAML 文件中以显示某些中间结果。 即使仅用于显示对象类型，这仍然非常有用。

字符串格式页面说明了 `StringFormat` 属性的多种用法：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

`Slider` 和 `TimePicker` 上的绑定显示了特定于 `double` 和 `TimeSpan` 数据类型的格式规范的用法。 显示 `Entry` 视图中文本的 `StringFormat` 演示了如何使用 `&quot;` HTML 实体在格式设置字符串中指定双引号。

XAML 文件的下一部分是 `StackLayout`，其中 `BindingContext` 设置为引用静态 `DateTime.Now` 属性的 `x:Static` 标记扩展。 第一个绑定不具有属性：

```xaml
<Label Text="{Binding}" />
```

这只显示带有默认格式的 `BindingContext` 的 `DateTime` 值。 第二个绑定显示 `DateTime` 的 `Ticks` 属性，而其他两个绑定显示带有特定格式的 `DateTime` 本身。 请注意此 `StringFormat`：

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

如果需要在格式设置字符串中显示左大括号或右大括号，只需使用一对即可。

最后一部分将 `BindingContext` 设置为 `Math.PI` 的值，并以默认格式和两种不同类型的数字格式显示。

下面是正在运行的程序：

[![字符串格式设置](string-formatting-images/stringformatting-small.png "字符串格式设置")](string-formatting-images/stringformatting-large.png#lightbox "字符串格式设置")

## <a name="viewmodels-and-string-formatting"></a>Viewmodel 和字符串格式

当使用 `Label` 和 `StringFormat` 显示同为 ViewModel 目标的视图的值时，可以将绑定定义为从视图到 `Label` 或从 ViewModel 到 `Label`。 一般情况下第二种方法最佳，因为它验证了试图和 ViewModel 之间的绑定是否正常运作。

此方法展示在更好的颜色选择器示例中，其中使用与[绑定模式](binding-mode.md)一文中展示的简单颜色选择器程序使用的相同 ViewModel：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

现在有三对 `Slider` 和 `Label` 元素绑定到了 `HslColorViewModel` 对象中的相同的源属性中。 唯一的区别在于 `Label` 有 `StringFormat` 属性来显示每个 `Slider` 值。

[![更出色的颜色选择器](string-formatting-images/bettercolorselector-small.png "更出色的颜色选择器")](string-formatting-images/bettercolorselector-large.png#lightbox "更出色的颜色选择器")

你可能想知道如何在传统的两位数十六进制格式中显示 RGB（红、绿、蓝）值。 这些整数值不是从 `Color` 结构中直接提供的。 一种解决方案是计算 ViewModel 中的颜色组件的整数值，并将其作为属性公开。 然后可以使用 `X2` 格式规范对这些值进行格式设置。

另一种方法更常用：可以编写一个绑定值转换器，在后文[**绑定值转换器**](converters.md)中有所描述。

但下一篇文章更详细地探讨了[绑定路径](binding-path.md)并展示了如何使用它来引用子属性和集合中的项。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 书中的数据绑定章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
