---
title: Xamarin CarouselView 数据
description: 使用数据填充 CarouselView，方法是将其 System.windows.controls.itemscontrol.itemssource 属性设置为任何实现 IEnumerable 的集合。
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/17/2019
ms.openlocfilehash: 8ec66a8d39f373b624e3a597e62014e3b1c72f56
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480562"
---
# <a name="xamarinforms-carouselview-data"></a>Xamarin CarouselView 数据

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)包括以下属性，这些属性定义要显示的数据及其外观：

- `IEnumerable`类型[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)中，指定要显示的项的集合，默认值为 "`null`"。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，指定要应用于要显示的项集合中的每个项的模板。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)定义一个 `ItemsUpdatingScrollMode` 属性，该属性表示在添加新项时 `CarouselView` 的滚动行为。 有关此属性的详细信息，请参阅[在添加新项时控制滚动位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)还可以在用户滚动时以增量方式加载数据。 有关详细信息，请参阅[以增量方式加载数据](#load-data-incrementally)。

## <a name="populate-a-carouselview-with-data"></a>使用数据填充 CarouselView

通过将数据的[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性设置为实现 `IEnumerable`的任何集合，使用数据填充[`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 可以通过从字符串数组中初始化 `ItemsSource` 属性，在 XAML 中添加项：

```xaml
<CarouselView>
    <CarouselView.ItemsSource>
        <x:Array Type="{x:Type x:String}">
            <x:String>Baboon</x:String>
            <x:String>Capuchin Monkey</x:String>
            <x:String>Blue Monkey</x:String>
            <x:String>Squirrel Monkey</x:String>
            <x:String>Golden Lion Tamarin</x:String>
            <x:String>Howler Monkey</x:String>
            <x:String>Japanese Macaque</x:String>
        </x:Array>
    </CarouselView.ItemsSource>
</CarouselView>
```

> [!NOTE]
> 请注意，`x:Array` 元素需要用于指示数组中项目类型的 `Type` 属性。

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!IMPORTANT]
> 如果需要在基础集合中添加、删除或更改项时刷新[`CarouselView`](xref:Xamarin.Forms.CarouselView) ，则基础集合应为发送属性更改通知的 `IEnumerable` 集合，如 `ObservableCollection`。

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView)水平显示项。 以下屏幕截图显示了在 iOS 和 Android 上显示不同字符串项的 `CarouselView`：

[![在 iOS 和 Android 上包含文本项的 CarouselView 的屏幕截图](populate-data-images/text.png "CarouselView 中的文本项")](populate-data-images/text-large.png#lightbox "CarouselView 中的文本项")

有关如何更改[`CarouselView`](xref:Xamarin.Forms.CarouselView)方向的信息，请参阅[Xamarin CarouselView 布局](layout.md)。 若要了解如何在 `CarouselView`中定义每个项的外观，请参阅[定义项外观](#define-item-appearance)。

### <a name="data-binding"></a>数据绑定

通过使用数据绑定将其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性绑定到 `IEnumerable` 集合，可以使用数据填充[`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 在 XAML 中，这是通过 `Binding` 标记扩展实现的：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此示例中， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性数据绑定到连接的 viewmodel 的 `Monkeys` 属性。

> [!NOTE]
> 可以启用编译的绑定以提高 Xamarin. Forms 应用程序中的数据绑定性能。 有关详细信息，请参阅[已编译的绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="define-item-appearance"></a>定义项外观

可以通过将[`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，来定义[`CarouselView`](xref:Xamarin.Forms.CarouselView)中每个项的外观：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    Label nameLabel = new Label { ... };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Image image = new Image { ... };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label locationLabel = new Label { ... };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Label detailsLabel = new Label { ... };
    detailsLabel.SetBinding(Label.TextProperty, "Details");

    StackLayout stackLayout = new StackLayout
    {
        Children = { nameLabel, image, locationLabel, detailsLabel }
    };

    Frame frame = new Frame { ... };
    StackLayout rootStackLayout = new StackLayout
    {
        Children = { frame }
    };

    return rootStackLayout;
});
```

在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中指定的元素定义 `CarouselView`中的每个项的外观。 在此示例中，`DataTemplate` 中的布局由[`StackLayout`](xref:Xamarin.Forms.StackLayout)管理，并且数据显示有一个[`Image`](xref:Xamarin.Forms.Image)对象和三个[`Label`](xref:Xamarin.Forms.Label)对象，这些对象都绑定到 `Monkey` 类的属性：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

以下屏幕截图显示每个项目的模板化结果：

[![IOS 和 Android 上每个项的模板 CarouselView 的屏幕截图](populate-data-images/datatemplate.png "CarouselView 中的模板化项")](populate-data-images/datatemplate-large.png#lightbox "CarouselView 中的模板化项")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在运行时选择项外观

可以在运行时根据项值选择[`CarouselView`](xref:Xamarin.Forms.CarouselView)中每个项的外观，方法是将[`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置为[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)对象：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls"
             x:Class="CarouselViewDemos.Views.HorizontalLayoutDataTemplateSelectorPage">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CarouselView ItemsSource="{Binding Monkeys}"
                  ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置为 `MonkeyDataTemplateSelector` 对象。 下面的示例演示了 `MonkeyDataTemplateSelector` 类：

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

`MonkeyDataTemplateSelector` 类定义设置为不同数据模板 `AmericanMonkey` 和 `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)属性。 当猴子名称包含 "美洲" 时，`OnSelectTemplate` 重写将返回 `AmericanMonkey` 模板。 当猴子名称中不包含 "美洲" 时，`OnSelectTemplate` 重写将返回 `OtherMonkey` 模板，该模板显示其数据灰显：

[![IOS 和 Android 上的 CarouselView 运行时项模板选择屏幕截图](populate-data-images/datatemplateselector.png "CarouselView 中的运行时项模板选择")](populate-data-images/datatemplateselector-large.png#lightbox "CarouselView 中的运行时项模板选择")

有关数据模板选择器的详细信息，请参阅[Create a Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用[`CarouselView`](xref:Xamarin.Forms.CarouselView)时，切勿将[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象的根元素设置为 `ViewCell`。 这将导致引发异常，因为 `CarouselView` 没有单元的概念。

## <a name="display-indicators"></a>显示指示器

指示器，表示 `CarouselView`中的项数和当前位置，可以在 `CarouselView`旁显示。 这可以通过 `IndicatorView` 控件来实现：

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView IndicatorView.ItemsSourceBy="carouselView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

在此示例中，`IndicatorView` 呈现在 `CarouselView`下，并且 `CarouselView`中的每一项都有一个指示符。 使用数据填充 `IndicatorView`，方法是将 `ItemsSourceBy` 附加属性设置为 `CarouselView` 对象。 每个指示器都是浅灰色圆圈，而表示 `CarouselView` 中当前项的指示器是深灰色：

[![IOS 和 Android 上的 CarouselView 和 IndicatorView 屏幕截图](populate-data-images/indicators.png "IndicatorView 圆圈")](populate-data-images/indicators-large.png#lightbox "IndicatorView 圆圈")

> [!IMPORTANT]
> 设置 `IndicatorView.ItemsSourceBy` 附加属性会导致 `IndicatorView.Position` 属性绑定到 `CarouselView.Position` 属性，并将 `IndicatorView.ItemsSource` 属性绑定到 `CarouselView.ItemsSource` 属性。

有关指示器的详细信息，请参阅[Xamarin. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)。

## <a name="pull-to-refresh"></a>请求刷新

[`CarouselView`](xref:Xamarin.Forms.CarouselView)支持通过 `RefreshView`拉取到刷新功能，这样就可以通过下拉项来刷新要显示的数据。 `RefreshView` 是一种容器控件，该控件提供向其子级提供刷新功能的拉取，前提是子级支持可滚动的内容。 因此，通过将 `CarouselView` 设置为 `RefreshView`的子项来实现对该的拉取：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CarouselView ItemsSource="{Binding Animals}">
        ...
    </CarouselView>
</RefreshView>
```

等效 C# 代码如下：

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = carouselView;
// ...
```

当用户启动刷新时，将执行 `Command` 属性定义的 `ICommand`，该属性应刷新正在显示的项。 刷新发生时，会显示刷新可视化效果，其中包含动画进度圆：

[![IOS 和 Android 上的 CarouselView 请求刷新的屏幕截图](populate-data-images/pull-to-refresh.png "CarouselView 请求刷新")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView 请求刷新")

`RefreshView.IsRefreshing` 属性的值指示 `RefreshView`的当前状态。 用户触发刷新时，此属性会自动转换为 `true`。 刷新完成后，应将属性重置为 `false`。

有关 `RefreshView`的详细信息，请参阅[RefreshView](~/xamarin-forms/user-interface/refreshview.md)。

## <a name="load-data-incrementally"></a>以增量方式加载数据

[`CarouselView`](xref:Xamarin.Forms.CarouselView)支持在用户滚动项目时以增量方式加载数据。 这可以实现各种方案，例如，在用户滚动时，从 web 服务异步加载数据页。 此外，还可以配置加载更多数据的点，以便用户不会看到空白空间，也不会停止滚动。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义以下属性来控制数据的增量加载：

- `RemainingItemsThreshold`，类型 `int`，将在其中激发 `RemainingItemsThresholdReached` 事件的列表中尚未显示的项的阈值。
- `ICommand`类型的 `RemainingItemsThresholdReachedCommand`，在达到 `RemainingItemsThreshold` 时执行。
- `RemainingItemsThresholdReachedCommandParameter`，属于 `object` 类型，是传递给 `RemainingItemsThresholdReachedCommand` 的参数。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)还定义了一个 `RemainingItemsThresholdReached` 事件，当 `CarouselView` 滚动到足够多的 `RemainingItemsThreshold` 项尚未显示时，将触发该事件。 可以处理此事件以加载更多项。 此外，当激发 `RemainingItemsThresholdReached` 事件时，将执行 `RemainingItemsThresholdReachedCommand`，从而使增量数据加载在 viewmodel 中进行。

`RemainingItemsThreshold` 属性的默认值为-1，表示将永远不会激发 `RemainingItemsThresholdReached` 事件。 当属性值为0时，当显示[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)中的最后一项时，将激发 `RemainingItemsThresholdReached` 事件。 对于大于0的值，当 `ItemsSource` 包含尚未滚动到的项数时，将激发 `RemainingItemsThresholdReached` 事件。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)验证 `RemainingItemsThreshold` 属性，使其值始终大于或等于-1。

下面的 XAML 示例显示了一个以增量方式加载数据的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

```xaml
<CarouselView ItemsSource="{Binding Animals}"
              RemainingItemsThreshold="2"
              RemainingItemsThresholdReached="OnCarouselViewRemainingItemsThresholdReached"
              RemainingItemsThresholdReachedCommand="{Binding LoadMoreDataCommand}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    RemainingItemsThreshold = 2
};
carouselView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

在此代码示例中，当有2个项尚未滚动到并且在响应中执行 `OnCollectionViewRemainingItemsThresholdReached` 事件处理程序时，将激发 `RemainingItemsThresholdReached` 事件：

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 还可以通过将 `RemainingItemsThresholdReachedCommand` 绑定到 viewmodel 中的 `ICommand` 实现，以增量方式加载数据。

## <a name="related-links"></a>相关链接

- [CarouselView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
