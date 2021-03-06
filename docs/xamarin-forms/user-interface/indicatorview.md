---
title: Xamarin. Forms IndicatorView
description: IndicatorView 是一个控件，用于显示表示 CarouselView 中的项数和当前位置的指示器。
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2020
ms.openlocfilehash: e76cf6e766a95994fa2862deb9eb73928f4769a2
ms.sourcegitcommit: 5d22f37dfc358678df52a4d17c57261056a72cb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674526"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin. Forms IndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

`IndicatorView` 是一个控件，它在 `CarouselView`中显示表示项数和当前位置的指示器：

[![IOS 和 Android 上的 CarouselView 和 IndicatorView 屏幕截图](indicatorview-images/circles.png "IndicatorView 圆圈")](indicatorview-images/circles-large.png#lightbox "IndicatorView 圆圈")

`IndicatorView` 在 iOS 和 Android 平台上的 Xamarin 4.4 中提供，在4.5 的通用 Windows 平台中可用。 但是，它当前是实验性的，只能通过将以下代码行添加到 iOS 上的 `AppDelegate` 类，或在调用 `Forms.Init`之前添加到 Android 上的 `MainActivity` 类：

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` 定义以下属性：

- `Count`，类型 `int`，指示符的数目。
- `HideSingle`，类型 `bool`，指示指示器是否应仅在存在时隐藏。 默认值为 `true`。
- `IndicatorColor`，类型 `Color`，指示符的颜色。
- `double`类型的 `IndicatorSize`，指示器的大小。 默认值为6.0。
- `Layout<View>`类型的 `IndicatorLayout`定义用于呈现 `IndicatorView`的布局类。 此属性由 Xamarin 设置，并且通常不需要由开发人员设置。
- `IndicatorTemplate`类型 `DataTemplate`，用于定义每个指示器的外观的模板。
- `IndicatorShape`类型的 `IndicatorsShape`，每个指示器的形状。
- `ItemsSource`类型 `IEnumerable`，将为其显示指示器的集合。 设置 `CarouselView.IndicatorView` 属性时，将自动设置此属性。
- `MaximumVisible`，类型 `int`，可见指示器的最大数目。 默认值为 `int.MaxValue`。
- `Position`类型 `int`，即当前选定的指示器索引。 此属性使用 `TwoWay` 绑定。 设置 `CarouselView.IndicatorView` 属性时，将自动设置此属性。
- `Color`类型的 `SelectedIndicatorColor`，表示 `CarouselView`中当前项的指示器的颜色。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标和样式。

## <a name="create-an-indicatorview"></a>创建 IndicatorView

下面的示例演示如何在 XAML 中实例化 `IndicatorView`：

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

在此示例中，`IndicatorView` 呈现在 `CarouselView`下，并且 `CarouselView`中的每一项都有一个指示符。 使用数据填充 `IndicatorView`，方法是将 `CarouselView.IndicatorView` 属性设置为 `IndicatorView` 对象。 每个指示器都是浅灰色圆圈，而表示 `CarouselView` 中当前项的指示器是深灰色。

> [!IMPORTANT]
> 设置 `CarouselView.IndicatorView` 属性会导致 `IndicatorView.Position` 属性绑定到 `CarouselView.Position` 属性，并将 `IndicatorView.ItemsSource` 属性绑定到 `CarouselView.ItemsSource` 属性。

## <a name="change-indicator-shape"></a>更改指示器形状

`IndicatorView` 类具有一个 `IndicatorsShape` 属性，该属性确定指示器的形状。 此属性可设置为 `IndicatorShape` 枚举成员之一：

- `Circle` 指定指示器形状将为圆形。 这是 `IndicatorView.IndicatorsShape` 属性的默认值。
- `Square` 指示指示器形状将为正方形。

下面的示例演示了一个配置为使用方指示器的 `IndicatorView`：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>更改指示器大小

`IndicatorView` 类具有一个类型为 `double`的 `IndicatorSize` 属性，该属性确定标记在与设备无关的单位中的大小。 此属性的默认值为6.0。

下面的示例演示了一个配置为显示更大指示器的 `IndicatorView`：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>限制显示的指示器数量

`IndicatorView` 类具有一个类型为 `int`的 `MaximumVisible` 属性，该属性确定可见指示器的最大数目。

下面的示例演示了一个配置为最多显示六个指示器的 `IndicatorView`：

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>定义指示器外观

可以通过将 `IndicatorView.IndicatorTemplate` 属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)来定义每个指示器的外观：

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中指定的元素定义每个指示器的外观。 在此示例中，每个指示器都是使用 `FontImage` 标记扩展显示字体图标的[`Image`](xref:Xamarin.Forms.Image) 。

以下屏幕截图显示了使用字体图标呈现的指示器：

[![IOS 和 Android 上的模板化 IndicatorView 的屏幕截图](indicatorview-images/templated.png "模板化 IndicatorView")](indicatorview-images/templated-large.png#lightbox "模板化 IndicatorView")

有关 `FontImage` 标记扩展的详细信息，请参阅[FontImage 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)。

## <a name="related-links"></a>相关链接

- [IndicatorView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [FontImage 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
