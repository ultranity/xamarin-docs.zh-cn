---
title: Xamarin CarouselView 滚动
description: 当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此外，CarouselView 还定义了两个 ScrollTo 方法，这些方法以编程方式将项滚动到视图中。
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
ms.openlocfilehash: 735a572f4aadfc224e545e371525b96f29c9552e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635902"
---
# <a name="xamarinforms-carouselview-scrolling"></a>Xamarin CarouselView 滚动

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义以下与滚动相关的属性：

- `HorizontalScrollBarVisibility`，类型为 `ScrollBarVisibility`，用于指定水平滚动条可见的时间。
- `bool`类型的 `IsDragging`，指示 `CarouselView` 是否正在滚动。 这是一个只读属性，其默认值为 `false`。
- `IsScrollAnimated`类型 `bool`，用于指定滚动 `CarouselView`时是否会发生动画。 默认值是 `true`。
- `ItemsUpdatingScrollMode`类型的 `ItemsUpdatingScrollMode`，它表示在添加新项时 `CarouselView` 的滚动行为。
- `ScrollBarVisibility`类型的 `VerticalScrollBarVisibility`，它指定垂直滚动条何时可见。

所有这些属性都是[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)还定义了两个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，这些方法可将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中，而另一个重载将指定项滚动到视图中。 这两个重载都有其他参数，可以指定该参数以指示滚动完成后项的准确位置，以及是否对滚动进行动画处理。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义一个[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件，当调用其中一个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时，将触发该事件。 `ScrollToRequested` 事件附带的[`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)对象具有多个属性，包括 `IsAnimated`、`Index`、`Item`和 `ScrollToPosition`。 这些属性是从 `ScrollTo` 方法调用中指定的参数设置的。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)会定义一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 `Scrolled` 事件附带的 `ItemsViewScrolledEventArgs` 对象具有多个属性。 有关详细信息，请参阅[检测滚动](#detect-scrolling)。

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时，项会对齐到位置。 有关详细信息，请参阅[对齐点](#snap-points)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)还可以在用户滚动时以增量方式加载数据。 有关详细信息，请参阅[以增量方式加载数据](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>检测滚动

可以检查 `IsDragging` 属性，以确定[`CarouselView`](xref:Xamarin.Forms.CarouselView)当前是否正在滚动项目。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)会定义一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 当需要有关滚动的数据时，应使用此事件。

下面的 XAML 示例显示了一个 `CarouselView`，该为 `Scrolled` 事件设置事件处理程序：

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

在此代码示例中，当引发 `Scrolled` 事件时，将执行 `OnCarouselViewScrolled` 事件处理程序：

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

在此示例中，`OnCarouselViewScrolled` 事件处理程序输出事件随附的 `ItemsViewScrolledEventArgs` 对象的值。

> [!IMPORTANT]
> 对于用户启动的滚动以及以编程方式滚动，将触发 `Scrolled` 事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>将索引中的项滚动到视图中

第一个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法重载将指定索引处的项滚动到视图中。 给定一个名为 `carouselView`的[`CarouselView`](xref:Xamarin.Forms.CarouselView)对象，下面的示例演示如何将索引6处的项滚动到视图中：

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> 调用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时，将激发[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="scroll-an-item-into-view"></a>将项滚动到视图

第二个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法重载将指定项滚动到视图中。 给定一个名为 `carouselView`的[`CarouselView`](xref:Xamarin.Forms.CarouselView)对象，下面的示例演示如何将 Proboscis 的猴子项滚动到视图中：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> 调用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时，将激发[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="disable-scroll-animation"></a>禁用滚动动画

在[`CarouselView`](xref:Xamarin.Forms.CarouselView)中的项之间移动时，将显示滚动动画。 此动画同时用于用户启动滚动和以编程方式滚动。 将 `IsScrollAnimated` 属性设置为 "`false` 将对这两个滚动类别禁用动画。

或者，可以将 `ScrollTo` 方法的 `animate` 参数设置为 `false`，以在编程滚动时禁用滚动动画：

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制滚动位置

在将某个项滚动到视图中时，可以使用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法的 `position` 参数指定该项在滚动完成后的确切位置。 此参数接受[`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition)枚举成员。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成员指示应滚动项，直到它在视图中可见：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此示例代码将导致滚动项进入视图所需的最少滚动。

> [!NOTE]
> 默认情况下，如果在调用 `ScrollTo` 方法时未指定 `position` 参数，则默认情况下使用[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成员。

### <a name="start"></a>开始

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的开头：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此示例代码会将项滚动到视图的开头。

### <a name="center"></a>中心

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的中心：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此示例代码会将项滚动到视图的中心。

### <a name="end"></a>结束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的末尾：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此示例代码会将项滚动到视图末尾。

## <a name="control-scroll-position-when-new-items-are-added"></a>在添加新项时控制滚动位置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义 `ItemsUpdatingScrollMode` 属性，该属性由可绑定的属性支持。 此属性获取或设置一个 `ItemsUpdatingScrollMode` 枚举值，该值表示在添加新项时 `CarouselView` 的滚动行为。 `ItemsUpdatingScrollMode` 枚举定义下列成员：

- `KeepItemsInView` 调整滚动偏移量，以便在添加新项时保持显示第一个可见项。
- `KeepScrollOffset` 在添加新项时，使滚动偏移量相对于列表的开头保持。
- `KeepLastItemInView` 调整滚动偏移量，以便在添加新项时使最后一项可见。

`ItemsUpdatingScrollMode` 属性的默认值为 `KeepItemsInView`。 因此，当新项添加到[`CarouselView`](xref:Xamarin.Forms.CarouselView)时，列表中的第一个可见项仍将显示。 若要确保新添加的项始终显示在列表的底部，应将 `ItemsUpdatingScrollMode` 属性设置为 `KeepLastItemInView`：

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>滚动条可见性

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 属性，这些属性由可绑定属性支持。 这些属性获取或设置一个[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)枚举值，该值表示水平或垂直滚动条何时可见。 `ScrollBarVisibility` 枚举定义下列成员：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)指示平台的默认滚动条行为，是 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 属性的默认值。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)指示滚动条可见，即使在视图中显示了内容也是如此。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)指示滚动条将不可见，即使内容无法在视图中显示也是如此。

## <a name="snap-points"></a>对齐点

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时，项会对齐到位置，并且由[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类中的以下属性控制：

- [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)类型的[`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)指定滚动时对齐点的行为。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)类型的[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)指定对齐点如何与项对齐。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> 发生对齐时，它将以生成最小动作量的方向发生。

### <a name="snap-points-type"></a>对齐点类型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)枚举定义以下成员：

- `None` 指示滚动不会对齐到项。
- `Mandatory` 指示内容始终对齐到最接近的对齐点，在该点上，滚动沿惯性方向自然停止。
- `MandatorySingle` 指示与 `Mandatory`相同的行为，但一次只滚动一项。

默认情况下，在[`CarouselView`](xref:Xamarin.Forms.CarouselView)上， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为 `SnapPointsType.MandatorySingle`，这可确保滚动一次只滚动一项。

以下屏幕截图显示了一个关闭对齐的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

[![IOS 和 Android 上不带对齐点的 CarouselView 的屏幕截图](scrolling-images/snappoints-none.png "不带对齐点的 CarouselView")](scrolling-images/snappoints-none-large.png#lightbox "不带对齐点的 CarouselView")

### <a name="snap-points-alignment"></a>对齐点对齐

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)枚举定义 `Start`、`Center`和 `End` 成员。

> [!IMPORTANT]
> 仅当[`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为 `Mandatory`或 `MandatorySingle`时，才考虑[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性的值。

#### <a name="start"></a>开始

`SnapPointsAlignment.Start` 成员指示对齐点与项的开头边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

当用户 swipes 在水平滚动[`CarouselView`](xref:Xamarin.Forms.CarouselView)中启动滚动时，左侧项目将与视图的左侧对齐：

[![IOS 和 Android 上的 CarouselView 与开始对齐点的屏幕截图](scrolling-images/snappoints-start.png "具有开始对齐点的 CarouselView")](scrolling-images/snappoints-start-large.png#lightbox "具有开始对齐点的 CarouselView")

#### <a name="center"></a>中心

`SnapPointsAlignment.Center` 成员指示对齐点与项的中心对齐。

默认情况下，在[`CarouselView`](xref:Xamarin.Forms.CarouselView)上， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性设置为 "`Center`"。 但是，为了完整起见，以下 XAML 示例显示了如何设置此枚举成员：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

当用户 swipes 在水平滚动[`CarouselView`](xref:Xamarin.Forms.CarouselView)中启动滚动时，中心项将与视图中心对齐：

[![IOS 和 Android 上包含中心对齐点的 CarouselView 的屏幕截图](scrolling-images/snappoints-center.png "具有中心对齐点的 CarouselView")](scrolling-images/snappoints-center-large.png#lightbox "具有中心对齐点的 CarouselView")

#### <a name="end"></a>结束

`SnapPointsAlignment.End` 成员指示对齐点与项的尾部边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

当用户 swipes 在水平滚动[`CarouselView`](xref:Xamarin.Forms.CarouselView)中启动滚动时，右侧项目将与视图的右侧对齐。

[![IOS 和 Android 上带有结束对齐点的 CarouselView 的屏幕截图](scrolling-images/snappoints-end.png "结束对齐点的 CarouselView")](scrolling-images/snappoints-end-large.png#lightbox "结束对齐点的 CarouselView")

## <a name="related-links"></a>相关链接

- [CarouselView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
