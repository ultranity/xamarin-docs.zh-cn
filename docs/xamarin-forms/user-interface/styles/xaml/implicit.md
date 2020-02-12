---
title: 在 Xamarin.Forms 中的隐式样式
description: 隐式样式是指可供所有控件的相同的目标类型，而无需每个控件以引用样式。
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: d58ba81596cccf470b7246514d71f35968599880
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131058"
---
# <a name="implicit-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的隐式样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_隐式样式是同一 TargetType 的所有控件使用的样式，无需每个控件都引用该样式。_

## <a name="create-an-implicit-style-in-xaml"></a>在 XAML 中创建隐式样式

若要声明页面级别的[`Style`](xref:Xamarin.Forms.Style) ，必须将[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)添加到页面，然后一个或多个 `Style` 声明可以包含在 `ResourceDictionary`中。 通过未指定 `x:Key` 特性使 `Style` 成为*隐式*的。 然后，样式将应用于与 `TargetType` 完全匹配的视觉元素，但不会应用于从 `TargetType` 值派生的元素。

下面的代码示例演示在页的 `ResourceDictionary`中的 XAML 中声明的*隐式*样式，并将其应用于页面的[`Entry`](xref:Xamarin.Forms.Entry)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)定义了应用于页面的[`Entry`](xref:Xamarin.Forms.Entry)实例的单个*隐式*样式。 `Style` 用于在黄色背景上显示蓝色文本，同时还设置其他外观选项。 `Style` 将添加到页面的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中，而无需指定 `x:Key` 属性。 因此，`Style` 将隐式应用于所有 `Entry` 实例，因为它们与 `Style` 的[`TargetType`](xref:Xamarin.Forms.Style.TargetType)属性完全匹配。 但 `Style` 不会应用于 `CustomEntry` 实例，该实例是 `Entry`的子类。 这会导致如以下屏幕截图中所示的外观：

[![隐式样式示例](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

此外，第四个[`Entry`](xref:Xamarin.Forms.Entry)会将隐式样式的[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)和[`TextColor`](xref:Xamarin.Forms.InputView.TextColor)属性重写为不同的 `Color` 值。

### <a name="create-an-implicit-style-at-the-control-level"></a>在控件级别创建隐式样式

除了在页面级别创建*隐式*样式以外，还可以在控件级别创建它们，如下面的代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，*隐式* [`Style`](xref:Xamarin.Forms.Style)分配给[`StackLayout`](xref:Xamarin.Forms.StackLayout)控件的[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)集合。 然后，可以将*隐式*样式应用于控件及其子级。

有关在应用程序的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中创建样式的信息，请参阅[全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-implicit-style-in-c35"></a>在 C 中创建隐式样式&#35;

可以通过创建新的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，然后将 `Style` 实例添加C#到 `ResourceDictionary`中，将[`Style`](xref:Xamarin.Forms.Style)实例添加到页面的[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)集合中，如下面的代码示例所示：

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

构造函数定义应用于页面的[`Entry`](xref:Xamarin.Forms.Entry)实例的单个*隐式*样式。 `Style` 用于在黄色背景上显示蓝色文本，同时还设置其他外观选项。 `Style` 将添加到页面的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中，而无需指定 `key` 字符串。 因此，`Style` 将隐式应用于所有 `Entry` 实例，因为它们与 `Style` 的[`TargetType`](xref:Xamarin.Forms.Style.TargetType)属性完全匹配。 但 `Style` 不会应用于 `CustomEntry` 实例，该实例是 `Entry`的子类。

## <a name="apply-a-style-to-derived-types"></a>将样式应用于派生类型

[`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)属性可将样式应用于派生自[`TargetType`](xref:Xamarin.Forms.Style.TargetType)属性所引用的基类型的控件。 因此，将此属性设置为 `true` 可启用单个样式以面向多个类型，前提是这些类型派生自 `TargetType` 属性中指定的基类型。

下面的示例演示了一个隐式样式，该样式将[`Button`](xref:Xamarin.Forms.Button)实例的背景色设置为红色：

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

将此样式置于页面级[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)将导致其应用于页面上的所有[`Button`](xref:Xamarin.Forms.Button)实例，还会应用于从 `Button`派生的任何控件。 但是，如果[`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)属性保持未设置，则样式仅应用于 `Button` 实例。

等效 C# 代码如下：

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
