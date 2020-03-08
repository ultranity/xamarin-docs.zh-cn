---
title: 自定义 ListView 单元格的外观
description: 本文探讨了表示 Xamarin.Forms 应用程序中的数据，同时利用 ListView 控件的方便的选项。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917262"
---
# <a name="customizing-listview-cell-appearance"></a>自定义 ListView 单元格的外观

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

Xamarin [`ListView`](xref:Xamarin.Forms.ListView)类用于显示可滚动列表，可通过使用 `ViewCell` 元素进行自定义。 `ViewCell` 元素可以显示文本和图像，指示真/假状态和接收用户输入。

## <a name="built-in-cells"></a>内置的单元格
Xamarin 提供适用于许多应用程序的内置单元：

- [`TextCell`](#textcell)控件用于显示文本，其中包含可选的第二行详细信息文本。
- [`ImageCell`](#imagecell)控件类似于 `TextCell`，但在文本左侧包含一个图像。
- `SwitchCell` 控件用于显示和捕获开启/关闭或真/假状态。
- `EntryCell` 控件用于显示用户可编辑的文本数据。

[`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell)和[`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell)控件更常见地用于[`TableView`](~/xamarin-forms/user-interface/tableview.md)的上下文中。

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell)是用于显示文本的单元格，可选择使用第二行作为详细信息文本。 以下屏幕截图显示 iOS 和 Android 上的 `TextCell` 项：

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

TextCells 在运行时呈现为本机控件，因此与自定义 `ViewCell`相比，性能非常好。 TextCells 可自定义，允许你设置以下属性：

- `Text` &ndash; 以大字体显示在第一行中的文本。
- `Detail` &ndash; 以较小字体显示在第一行下显示的文本。
- `TextColor` &ndash; 文本的颜色。
- `DetailColor` &ndash; 详细信息文本的颜色

以下屏幕截图显示了具有自定义颜色属性 `TextCell` 项：

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)（如 `TextCell`）可用于显示文本和辅助详细信息文本，并且通过使用每个平台的本机控件提供了极佳的性能。 `ImageCell` 与 `TextCell` 不同，因为它在文本左侧显示图像。

以下屏幕截图显示 iOS 和 Android 上的 `ImageCell` 项： !["Default ImageCell Example"](customizing-cell-appearance-images/image-cell-default.png "默认 ImageCell 示例")

当您需要显示具有可视方位的数据列表（如联系人或电影列表）时，`ImageCell` 非常有用。 `ImageCell`可自定义，允许设置：

- `Text` &ndash; 在第一行中显示的文本，采用大字体
- `Detail` &ndash; 以较小字体显示在第一行下显示的文本
- `TextColor` &ndash; 文本颜色
- `DetailColor` &ndash; 详细信息文本的颜色
- `ImageSource` &ndash; 要显示在文本旁边的图像

以下屏幕截图显示了具有自定义颜色属性 `ImageCell` 项： !["自定义的 ImageCell 示例"](customizing-cell-appearance-images/image-cell-custom.png "自定义的 ImageCell 示例")

## <a name="custom-cells"></a>自定义单元格
自定义单元使您能够创建内置单元不支持的单元布局。 例如，你可能想要提供两个标签具有相等的权重的单元格。 `TextCell` 的权限不足，因为 `TextCell` 有一个较小的标签。 大多数单元格自定义添加其他只读数据 （如其他标签、 图像或其他显示信息）。

所有自定义单元必须派生自[`ViewCell`](xref:Xamarin.Forms.ViewCell)，这是所有内置单元类型使用的相同基类。

Xamarin 在 `ListView` 控件上提供[缓存行为](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)，这可提高某些类型的自定义单元的滚动性能。

以下屏幕截图显示了自定义单元格的示例：

!["自定义单元示例"](customizing-cell-appearance-images/custom-cell.png "自定义单元格示例")

### <a name="xaml"></a>XAML
前面的屏幕截图中显示的自定义单元可通过以下 XAML 创建：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

XAML 的工作方式如下：

- 自定义单元嵌套在 `ListView.ItemTemplate`内的 `DataTemplate`中。 这与使用任何内置单元的过程相同。
- `ViewCell` 是自定义单元格的类型。 `DataTemplate` 元素的子元素必须是 `ViewCell` 类或派生自该类。
- 在 `ViewCell`中，布局可以由任何 Xamarin. Forms 布局进行管理。 在此示例中，布局由 `StackLayout`管理，这允许自定义背景色。

> [!NOTE]
> 可绑定 `StackLayout` 的任何属性都可以绑定到自定义单元中。 但 XAML 示例中未显示此功能。

### <a name="code"></a>代码

还可以在代码中创建自定义单元。 首先，必须创建一个派生自 `ViewCell` 的自定义类：

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

在页构造函数中，ListView 的 `ItemTemplate` 属性设置为指定了 `CustomCell` 类型的 `DataTemplate`：

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

### <a name="binding-context-changes"></a>绑定上下文的更改

绑定到自定义单元类型的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例时，显示 `BindableProperty` 值的 UI 控件应使用[`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged)重写来设置要显示在每个单元中的数据，而不是单元构造函数，如下面的代码示例所示：

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

当引发[`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged)事件时，将调用[`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged)重写，以响应[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)属性的值的更改。 因此，当 `BindingContext` 更改时，显示[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)值的 UI 控件应设置其数据。 请注意，应检查 `BindingContext` `null` 值，因为它可以通过 Xamarin 进行设置。垃圾回收窗体，进而导致调用 `OnBindingContextChanged` 重写。

或者，UI 控件可以绑定到[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例以显示其值，这将不再需要重写 `OnBindingContextChanged` 方法。

> [!NOTE]
> 重写 `OnBindingContextChanged`时，请确保调用基类的 `OnBindingContextChanged` 方法，以便注册的委托接收 `BindingContextChanged` 事件。

在 XAML 中，绑定到数据的自定义单元格类型可以实现的如下面的代码示例中所示：

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

这会将 `CustomCell` 实例中的 `Name`、`Age`和 `Location` 可绑定属性绑定到基础集合中每个对象的 `Name`、`Age`和 `Location` 属性。

在 C# 中的等效绑定以下的代码示例所示：

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

在 iOS 和 Android 上，如果[`ListView`](xref:Xamarin.Forms.ListView)为回收元素，并且自定义单元格使用自定义呈现器，则自定义呈现器必须正确实现属性更改通知。 当重复使用单元格时，将在绑定上下文更新到可用单元格时更改其属性值，并引发 `PropertyChanged` 事件。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 有关回收单元的详细信息，请参阅[缓存策略](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)。

## <a name="related-links"></a>相关链接

- [内置单元格（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自定义单元格（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [绑定上下文已更改（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
