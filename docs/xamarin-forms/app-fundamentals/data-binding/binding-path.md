---
title: Xamarin.Forms 绑定路径
description: 本文介绍如何使用 Xamarin.Forms 数据绑定通过 Binding 类的 Path 属性访问子属性和集合成员。
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 79d8df3300b302512a7de4140968dbc4c8e79abc
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940345"
---
# <a name="xamarinforms-binding-path"></a>Xamarin.Forms 绑定路径

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

在前面的所有数据绑定示例中，`Binding` 类的 [`Path`](xref:Xamarin.Forms.Binding.Path) 属性（或 `Binding` 标记扩展的 [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) 属性）已设置为单个属性。 实际上，可以将 `Path` 设置为“子属性”（属性的属性），也可以设置为集合的成员。

例如，假定你的页面包含 `TimePicker`：

```xaml
<TimePicker x:Name="timePicker">
```

`TimePicker` 的 `Time` 属性为 `TimeSpan` 类型，但你可能希望创建引用该 `TimeSpan` 值的 `TotalSeconds` 属性的数据绑定。 数据绑定如下：

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

`Time` 属性的类型是 `TimeSpan`，该类型具有 `TotalSeconds` 属性。 `Time` 和 `TotalSeconds` 属性之间仅用一个句点连接。 `Path` 字符串中的项始终引用属性，而不是这些属性的类型。

“路径变化”页中显示了该示例和其他几个示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=netstandard"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

在第二个 `Label` 中，绑定源是页面本身。 `Content` 属性为 `StackLayout` 类型，它具有 `IList<View>` 类型（具有指示子级数目的 `Count` 属性）的 `Children` 属性。

## <a name="paths-with-indexers"></a>使用索引器的路径

“路径变化”页中的第三个 `Label` 中的绑定引用 `System.Globalization` 命名空间中的 [`CultureInfo`](xref:System.Globalization.CultureInfo) 类：

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

源设置为静态 `CultureInfo.CurrentCulture` 属性，该属性是 `CultureInfo` 类型的对象。 该类定义 [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) 类型的名为 `DateTimeFormat` 的属性，该属性包含 `DayNames` 集合。 索引选择第四项。

第四个 `Label` 执行一些类似的操作，但这些操作仅针对与法国关联的区域性。 绑定的 `Source` 属性设置为具有构造函数的 `CultureInfo` 对象：

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

有关使用 XAML 指定构造函数参数的详细信息，请参阅[传递构造函数参数](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)。

最后一个示例与第二个相似，只不过它引用了 `StackLayout` 的一个子级：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

该子级是 `Label`，它具有含 `Length` 属性的 `String` 类型的 `Text` 属性。 第一个 `Label` 报告 `TimePicker` 中的 `TimeSpan` 集，因此当该文本更改时，最后的 `Label` 也随之更改。

下面是正在运行的程序：

[![路径变体](binding-path-images/pathvariations-small.png "路径变体")](binding-path-images/pathvariations-large.png#lightbox "路径变体")

## <a name="debugging-complex-paths"></a>调试复杂路径

复杂路径定义可能很难构造：你需要知道每个子属性的类型或集合中项的类型，以便正确地添加下一个子属性，但是类型本身不会出现在路径中。 较好的方式是以增量方式逐步构建路径并查看中间结果。 对于上一个示例，你可以从完全没有 `Path` 定义开始：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

这将显示绑定源的类型，即 `DataBindingDemos.PathVariationsPage`。 你知道 `PathVariationsPage` 派生自 `ContentPage`，所以它有一个 `Content` 属性：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

`Content` 属性的类型现在显示为 `Xamarin.Forms.StackLayout`。 将 `Children` 属性添加到 `Path` 中，类型为 `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`（它是 Xamarin.Forms 内部的类），显然是集合类型。 向它添加索引，类型为 `Xamarin.Forms.Label`。 按这种方式继续操作。

当 Xamarin.Forms 处理绑定路径时，它会在路径中实现 `INotifyPropertyChanged` 接口的任何对象上安装 `PropertyChanged` 处理程序。 例如，由于 `Text` 属性更改，最终的绑定对第一个 `Label` 中的更改作出反应。

如果绑定路径中的属性没有实现 `INotifyPropertyChanged`，那么对该属性的任何更改都将被忽略。 一些更改可能会使绑定路径完全无效，所以应只在属性和子属性字符串永远不会失效的情况下才使用这种技术。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 书中的数据绑定章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
