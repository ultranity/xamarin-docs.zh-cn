---
title: Xamarin.Forms 基本绑定
description: 本文介绍了如何使用 Xamarin.Forms 数据绑定，将两个对象之间的一对属性连接起来，其中至少有一个通常是用户界面对象。 这两个对象称为“目标”和“源”。
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2019
ms.custom: video
ms.openlocfilehash: 2227e2bd47a5b4960d28be67bac7947a4fb57a93
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912596"
---
# <a name="xamarinforms-basic-bindings"></a>Xamarin.Forms 基本绑定

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Xamarin.Forms 数据绑定连接两个对象之间的一对属性，其中至少有一个通常是用户界面对象。 这两个对象称为“目标”和“源”   ：

- “目标”是设置数据要绑定的对象（和属性）  。
- “源”是数据绑定引用的对象（和属性）  。

这种区别有时可能有点令人困惑：在最简单的情况下，数据从源流到目标，这意味着目标属性值是从源属性的值设置的。 但是，在某些情况下，数据也可以从目标流向源，或者双向流动。 为了避免混淆，请记住，即使目标提供数据而非接收数据，也始终是设置数据绑定的对象。

## <a name="bindings-with-a-binding-context"></a>使用绑定上下文绑定

虽然数据绑定通常完全在 XAML 中指定，但在代码中查看数据绑定也是有指导意义的。 “基本代码绑定”页包含具有 `Label` 和 `Slider` 的 XAML 文件  ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider` 在 0 到 360 范围中进行设置。 此计划的目的是通过操作 `Slider` 来旋转 `Label`。

如果没有数据绑定，你可以将 `Slider` 的 `ValueChanged` 事件设置为访问 `Slider` 的 `Value` 属性的事件处理程序，并将该值设置为 `Label` 的 `Rotation` 属性。 数据绑定使该作业自动化；不再需要其中的事件处理程序和代码。

可以在派生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 的任何类的实例上设置绑定，这些类包括 `Element`、`VisualElement`、`View` 和 `View` 派生物。  绑定始终在目标对象上进行设置。 绑定引用源对象。 要设置数据绑定，请使用目标类的以下两个成员：

- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性指定源对象。
- [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) 方法指定目标属性和源属性。

在本例中，`Label` 是绑定目标，`Slider` 是绑定源。 `Slider` 源中的更改会影响 `Label` 目标的旋转。 数据从源流向目标。

`BindableObject` 定义的 `SetBinding` 方法的参数类型为 [`BindingBase`](xref:Xamarin.Forms.BindingBase)，[`Binding`](xref:Xamarin.Forms.Binding) 类从该类派生而来，但 [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) 类还定义了其他 `SetBinding` 方法。 “基本代码绑定”示例中的代码隐藏文件使用此类中更简单的 [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) 扩展方法  。

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

`Label` 对象是绑定目标，因此它是设置该属性和调用该方法的对象。 `BindingContext` 属性指示绑定源 `Slider`。

`SetBinding` 方法在绑定目标上进行调用，但需同时指定目标属性和源属性。 目标属性指定为 `BindableProperty` 对象：`Label.RotationProperty`。 源属性指定为字符串，并指示 `Slider` 的 `Value` 属性。

`SetBinding` 方法揭示了数据绑定最重要的规则之一：

目标属性必须由可绑定属性支持  。

此规则意味着目标对象必须是从 `BindableObject` 派生的类的实例。 有关可绑定对象和绑定属性的概述，请参阅[“可绑定属性”](~/xamarin-forms/xaml/bindable-properties.md)一文  。

源属性没有此类规则，该属性指定为字符串。 在内部，反射用于访问实际属性。 然而，在这种特殊情况下，`Value` 属性还受可绑定属性支持。

代码可以稍微简化：`RotationProperty` 可绑定属性由 `VisualElement` 定义，并由 `Label` 和 `ContentPage` 继承，因此在 `SetBinding` 调用中不需要类名：

```csharp
label.SetBinding(RotationProperty, "Value");
```

但是，包含类名可以很好地提醒目标对象。

操做 `Slider` 时，`Label` 会相应地旋转：

[![基本代码绑定](basic-bindings-images/basiccodebinding-small.png "基本代码绑定")](basic-bindings-images/basiccodebinding-large.png#lightbox "基本代码绑定")

“基本 Xaml 绑定”页等同于“基本代码绑定”，不同之处在于它定义了 XAML 中的整个数据绑定   ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

正如在代码中一样，数据绑定是在目标对象（即 `Label`）上设置的。 涉及两个 XAML 标记扩展。 这些可立即通过大括号分隔符识别：

- 引用源对象（命名为 `slider` 的 `Slider`）需要 `x:Reference` 标记扩展。
- `Binding` 标记扩展将 `Label` 的 `Rotation` 属性链接到 `Slider` 的 `Value` 属性。

有关 XAML 标记扩展的详细信息，请参阅文章 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。 `x:Reference` 标记扩展由 [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) 类支持；`Binding` 由 [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) 类支持。 正如 XML 命名空间前缀所示，`x:Reference` 是 XAML 2009 规范的一部分，而 `Binding` 是 Xamarin.Forms 的一部分。 请注意，在大括号中没有出现引号。

在设置 `BindingContext` 时，很容易忘记 `x:Reference` 标记扩展。 错误地将属性直接设置为绑定源的名称很常见，如下所示：

```xaml
BindingContext="slider"
```

但这不正确。 该标记将 `BindingContext` 属性设置为 `string` 对象，该对象的字符拼写为“slider”！

请注意，源属性是由 `BindingExtension` 的 [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) 属性指定的，它对应于 [`Binding`](xref:Xamarin.Forms.Binding) 类的 [`Path`](xref:Xamarin.Forms.Binding.Path) 属性。

可以简化“基本 XAML 绑定”  页上显示的标记：XAML 标记扩展名（如 `x:Reference` 和 `Binding`）可以定义“内容属性”  属性，对于 XAML 标记扩展，这意味着不需要出现属性名称。 `Name` 属性是 `x:Reference` 的内容属性，`Path` 属性是 `Binding` 的内容属性，这意味着可以从表达式中删除它们：

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>不使用绑定上下文绑定

`BindingContext` 属性是数据绑定的重要组件，但并不总是必要的。 源对象可以在 `SetBinding` 调用或 `Binding` 标记扩展中指定。

“可选代码绑定”示例演示了这一点  。 XAML 文件类似于“基本代码绑定”示例，但定义 `Slider` 是为了控制 `Label` 的 `Scale` 属性  。 因此，`Slider` 在 &ndash;2 到 2 范围进行设置：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件设置与 `BindableObject` 定义的 [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) 方法的绑定。 该参数是 [`Binding`](xref:Xamarin.Forms.Binding) 类的[构造函数](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object))：

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

`Binding` 构造函数有 6 个参数，因此 `source` 参数是用命名参数指定的。 该参数是 `slider` 对象。

运行此程序可能会感到惊讶：

[![替代代码绑定](basic-bindings-images/alternativecodebinding-small.png "替代代码绑定")](basic-bindings-images/alternativecodebinding-large.png#lightbox "替代代码绑定")

左侧的 iOS 屏幕显示了页面首次出现时的屏幕外观。 `Label` 在哪里？

问题在于 `Slider` 的初始值为 0。 这将导致 `Label` 的 `Scale` 属性也设置为 0，覆盖其默认值 1。 这会导致 `Label` 在最初是不可见的。 正如 Android 的屏幕截图所示，你可以操作 `Slider`，使 `Label` 再次出现，但它最初的消失让人不安。

你将在[下一篇文章](binding-mode.md)中发现如何通过从 `Scale` 属性的默认值初始化 `Slider` 来避免此问题。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement) 类还定义了 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 属性，这些属性可以在水平和垂直方向上以不同方式缩放 `VisualElement`。

“替代 XAML 绑定”页完全显示出在 XAML 中的等效绑定  ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

现在，`Binding` 标记扩展有两个属性集，`Source` 和 `Path`，用逗号分隔。 如果你愿意，也可在同一行上显示这两个属性集：

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

`Source` 属性设置为嵌入的 `x:Reference` 标记扩展，否则具有与设置 `BindingContext` 相同的语法。 请注意，大括号中没有出现引号，这两个属性必须用逗号分隔。

`Binding` 标记扩展的内容属性是 `Path`，但是标记扩展的 `Path=` 部分只有在它是表达式中的第一个属性时才能被删除。 若要消除 `Path=` 部分，需要交换这两个属性：

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

尽管 XAML 标记扩展通常由大括号分隔，但它们也可以表示为对象元素：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

现在 `Source` 和 `Path` 属性是常规的 XAML 属性：值出现在引号中，属性之间没有逗号分隔。 `x:Reference` 标记扩展也可以成为对象元素：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

这种语法并不常见，但当涉及到复杂对象时，有时它也是必要的。

到目前为止显示的示例将 `Binding` 的 `BindingContext` 属性和 `Source` 属性设置为 `x:Reference` 标记扩展，以引用页面上的另一个视图。 这两个属性的类型是 `Object`，可以将它们设置为包含适合绑定源的属性的任何对象。

在前面的文章中，你会发现可以将 `BindingContext` 或 `Source` 属性设置为 `x:Static` 标记扩展，以引用静态属性或字段的值，或将 `StaticResource` 标记扩展设置为引用存储在资源字典中的对象，或直接指向对象，该对象通常是（但不总是）视图模型的实例。

还可以将 `BindingContext` 属性设置为 `Binding` 对象，以便 `Binding` 的 `Source` 和 `Path` 属性定义绑定上下文。

## <a name="binding-context-inheritance"></a>绑定上下文继承

在本文中，你已经看到可以使用 `BindingContext` 属性或 `Binding` 对象的 `Source` 属性指定源对象。 如果两者都已设置，则 `Binding` 的 `Source` 属性优先于 `BindingContext`。

`BindingContext` 属性具有一个极其重要的特性：

`BindingContext` 属性的设置是通过可视化树继承的。 

正如你所看到的，这对于简化绑定表达式非常方便，在某些情况下，&mdash; 特别是在模型-视图-视图模型 (MVVM) 场景中&mdash;，这是非常重要的。

“绑定上下文继承”示例是绑定上下文继承的简单演示  ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

`StackLayout` 的 `BindingContext` 属性设置为 `slider` 对象。 该绑定上下文由 `Label` 和 `BoxView` 继承，它们的 `Rotation` 属性都设置为 `Slider` 的 `Value` 属性：

[![绑定上下文继承](basic-bindings-images/bindingcontextinheritance-small.png "绑定上下文继承")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "绑定上下文继承")

在[下一篇文章](binding-mode.md)中，你将看到“绑定模式”如何更改目标对象和源对象之间的数据流  。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 书中的数据绑定章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Data-Binding/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
