---
title: Xamarin CollectionView 布局
description: 默认情况下，CollectionView 会在垂直列表中显示其项。 但是，可以指定垂直和水平列表以及网格。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2019
ms.openlocfilehash: 6c2b3d8bad621db3110fe25041125c5694f21180
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77130991"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin CollectionView 布局

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义控件布局的以下属性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类型的[`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout)中，指定要使用的布局。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)类型[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)，则指定要使用的项度量策略。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView)会在垂直列表中显示其项。 但是，可以使用以下任何布局：

- 垂直列表–在添加新项时垂直增长的单个列列表。
- 水平列表–在添加新项时水平增长的单个行列表。
- 垂直网格–在添加新项时垂直增长的多列网格。
- 水平网格–在添加新项时水平增长的多行网格。

可以通过将[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为从[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类派生的类来指定这些布局。 此类定义以下属性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)，类型为[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)指定添加项时[`CollectionView`](xref:Xamarin.Forms.CollectionView)展开的方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)类型的[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)指定对齐点如何与项对齐。
- [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)类型的[`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)指定滚动时对齐点的行为。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。 有关对齐点的详细信息，请参阅[Xamarin CollectionView 滚动](scrolling.md)指南中的 "[对齐点](scrolling.md#snap-points)"。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举定义以下成员：

- `Vertical` 指示[`CollectionView`](xref:Xamarin.Forms.CollectionView)在添加项目时将垂直扩展。
- `Horizontal` 指示[`CollectionView`](xref:Xamarin.Forms.CollectionView)在添加项目时水平展开。

`LinearItemsLayout` 类继承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类，并定义了一个类型 `double`的 `ItemSpacing` 属性，该属性表示每个项周围的空白区域。 此属性的默认值为0，其值必须始终大于或等于0。 `LinearItemsLayout` 类还定义静态 `Vertical` 和 `Horizontal` 成员。 这些成员可以分别用来创建垂直或水平列表。 或者，可以创建一个 `LinearItemsLayout` 对象，将[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定为一个参数。

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)类继承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类，并定义以下属性：

- `double`类型的 `VerticalItemSpacing`，表示每个项周围的垂直空白空间。 此属性的默认值为0，其值必须始终大于或等于0。
- `double`类型的 `HorizontalItemSpacing`，它表示每个项周围的水平空白空间。 此属性的默认值为0，其值必须始终大于或等于0。
- `int`类型的 `Span`，它表示要在网格中显示的列数或行数。 此属性的默认值为1，并且它的值必须始终大于或等于1。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)使用本机布局引擎来执行布局。

## <a name="vertical-list"></a>垂直列表

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView)会在垂直列表布局中显示其项。 因此，无需将[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为使用此布局：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

但是，为了完整性，可以通过将[`CollectionView`](xref:Xamarin.Forms.CollectionView)的[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为 `VerticalList`，将其设置为在垂直列表中显示其项：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

此外，也可以通过将[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为 `LinearItemsLayout` 对象来完成此操作，将 `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定为 `Orientation` 属性值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

这会生成单个列列表，该列表将在添加新项时垂直增长：

[![IOS 和 Android 上的 CollectionView 垂直列表布局屏幕截图](layout-images/vertical-list.png "CollectionView 垂直列表布局")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直列表布局")

## <a name="horizontal-list"></a>水平列表

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为 `HorizontalList`，在水平列表中显示其项：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

此外，还可以通过将[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为 `LinearItemsLayout` 对象来实现此布局，并指定 `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员作为 `Orientation` 属性值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

这会生成单个行列表，该列表在添加新项时水平增长：

[![IOS 和 Android 上的 CollectionView 水平列表布局屏幕截图](layout-images/horizontal-list.png "CollectionView 水平列表布局")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水平列表布局")

## <a name="vertical-grid"></a>垂直网格

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为其[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)属性设置为 `Vertical`的[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)对象，在垂直网格中显示其项：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

默认情况下，垂直[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)将显示单个列中的项。 但是，此示例将 `GridItemsLayout.Span` 属性设置为2。 这会生成一个两列网格，这会在添加新项时垂直增长：

[![IOS 和 Android 上的 CollectionView 垂直网格布局的屏幕截图](layout-images/vertical-grid.png "CollectionView 垂直网格布局")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直网格布局")

## <a name="horizontal-grid"></a>水平网格

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为其[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)属性设置为 `Horizontal`的[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)对象，在水平网格中显示其项：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

默认情况下，水平[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)将在单个行中显示项。 但是，此示例将 `GridItemsLayout.Span` 属性设置为4。 这会导致四行网格，这会在添加新项时水平增长：

[![IOS 和 Android 上的 CollectionView 水平网格布局屏幕截图](layout-images/horizontal-grid.png "CollectionView 水平网格布局")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水平网格布局")

## <a name="headers-and-footers"></a>页眉和页脚

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以显示与列表中的项一起滚动的页眉和页脚。 页眉和页脚可以是字符串、视图或[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)为指定页眉和页脚定义以下属性：

- `Header`类型 `object`，指定将显示在列表开头的字符串、绑定或视图。
- `HeaderTemplate`类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，指定用于设置 `Header`格式的 `DataTemplate`。
- `Footer`类型 `object`，指定将显示在列表末尾的字符串、绑定或视图。
- `FooterTemplate`类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，指定用于设置 `Footer`格式的 `DataTemplate`。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

将标题添加到水平增长的布局时，将在列表的左侧显示页眉。 同样，在将页脚添加到水平增长的布局时，将在列表右侧显示页脚。

### <a name="display-strings-in-the-header-and-footer"></a>在页眉和页脚中显示字符串

`Header` 和 `Footer` 属性可以设置为 `string` 值，如下面的示例中所示：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此代码将产生以下屏幕截图，其中的标头显示在 iOS 屏幕截图中，在 Android 屏幕截图中显示了页脚：

[![IOS 和 Android 上的 CollectionView 字符串标头和脚注的屏幕截图](layout-images/header-footer-string.png "CollectionView 字符串标头和表尾")](layout-images/header-footer-string-large.png#lightbox "CollectionView 字符串标头和表尾")

### <a name="display-views-in-the-header-and-footer"></a>在页眉和页脚中显示视图

`Header` 和 `Footer` 属性都可以设置为视图。 这可以是单个视图，也可以是包含多个子视图的视图。 下面的示例演示每个 `Header` 和 `Footer` 属性设置为包含[`Label`](xref:Xamarin.Forms.Label)对象的[`StackLayout`](xref:Xamarin.Forms.StackLayout)对象：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此代码将产生以下屏幕截图，其中的标头显示在 iOS 屏幕截图中，在 Android 屏幕截图中显示了页脚：

[![在 iOS 和 Android 上使用视图的 CollectionView 标头和脚注的屏幕截图](layout-images/header-footer-view.png "CollectionView 视图页眉和页脚")](layout-images/header-footer-view-large.png#lightbox "CollectionView 视图页眉和页脚")

### <a name="display-a-templated-header-and-footer"></a>显示模板化页眉和页脚

可以将 `HeaderTemplate` 和 `FooterTemplate` 属性设置为用于设置页眉和页脚格式的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象。 在此方案中，`Header` 和 `Footer` 属性必须绑定到要应用的模板的当前源，如以下示例中所示：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此代码将产生以下屏幕截图，其中的标头显示在 iOS 屏幕截图中，在 Android 屏幕截图中显示了页脚：

[![在 iOS 和 Android 上使用模板的 CollectionView 标头和脚注的屏幕截图](layout-images/header-footer-template.png "CollectionView 模板页眉和页脚")](layout-images/header-footer-template-large.png#lightbox "CollectionView 模板页眉和页脚")

## <a name="item-spacing"></a>项间距

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView)中的每一项周围没有任何空白区域。 可以通过在 `CollectionView`使用的项布局上设置属性来更改此行为。

当[`CollectionView`](xref:Xamarin.Forms.CollectionView)将其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为一个 `LinearItemsLayout` 对象时，`LinearItemsLayout.ItemSpacing` 属性可设置为表示每个项周围空白空间的 `double` 值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `LinearItemsLayout.ItemSpacing` 属性具有一个验证回调集，它可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

此代码将生成垂直的单列列表，每个项周围的间距为20：

[![在 iOS 和 Android 上具有项间距的 CollectionView 的屏幕截图](layout-images/vertical-list-spacing.png "CollectionView 项间距")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 项间距")

当[`CollectionView`](xref:Xamarin.Forms.CollectionView)将其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)属性设置为一个[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)对象时，可以将 `GridItemsLayout.VerticalItemSpacing` 和 `GridItemsLayout.HorizontalItemSpacing` 属性设置为 `double` 在每个项周围垂直和水平表示空白空间的值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `GridItemsLayout.VerticalItemSpacing` 和 `GridItemsLayout.HorizontalItemSpacing` 属性设置了验证回叫，这可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

此代码生成垂直的两列网格，每个项周围的垂直间距为20，每个项周围的水平间距为30：

[![Android 上具有项间距的 CollectionView 的屏幕截图](layout-images/vertical-grid-spacing.png "CollectionView 项间距")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 项间距")

## <a name="item-sizing"></a>项大小调整

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView)中的每个项分别进行测量和调整大小，前提是[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的 UI 元素不指定固定的大小。 此行为（可更改）由[`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)属性值指定。 此属性值可以设置为[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)枚举成员之一：

- `MeasureAllItems` –每个项都单独进行度量。 这是默认值。
- `MeasureFirstItem` –仅度量第一项，并为所有后续项提供与第一个项相同的大小。

> [!IMPORTANT]
> 如果在项大小旨在跨所有项进行统一，则使用 `MeasureFirstItem` 大小调整策略会提高性能。

下面的代码示例演示如何设置[`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)属性：

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>动态调整项的大小

[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的项可以在运行时通过更改[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中元素的与布局相关的属性，在运行时动态调整大小。 例如，下面的代码示例更改[`Image`](xref:Xamarin.Forms.Image)对象的[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)和[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)属性：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

`OnImageTapped` 事件处理程序将执行，以响应点击的[`Image`](xref:Xamarin.Forms.Image)对象，并更改图像的尺寸，以便更轻松地进行查看：

[![在 iOS 和 Android 上动态调整项大小的 CollectionView 屏幕截图](layout-images/runtime-resizing.png "CollectionView 动态项大小调整")](layout-images/runtime-resizing-large.png#lightbox "CollectionView 动态项大小调整")

## <a name="right-to-left-layout"></a>从右到左布局

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)属性设置为[`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)，按从右到左的流方向布局其内容。 但是，在理想情况下，应在页面或根布局上设置 `FlowDirection` 属性，这会使页面或根布局中的所有元素响应流方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

具有父级的元素的默认[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)为[`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此， [`CollectionView`](xref:Xamarin.Forms.CollectionView)从[`StackLayout`](xref:Xamarin.Forms.StackLayout)继承 `FlowDirection` 属性值，而该属性值又从[`ContentPage`](xref:Xamarin.Forms.ContentPage)继承 `FlowDirection` 属性值。 这会导致从右到左的布局，如以下屏幕截图所示：

[![IOS 和 Android 上的 CollectionView 从右到左垂直列表布局的屏幕截图](layout-images/vertical-list-rtl.png "CollectionView 从右到左垂直列表布局")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 从右到左垂直列表布局")

有关流方向的详细信息，请参阅[从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin CollectionView 滚动](scrolling.md)
