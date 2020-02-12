---
title: Xamarin. Forms CollectionView EmptyView
description: 在 CollectionView 中，可以指定一个空视图，在没有可显示的数据时向用户提供反馈。 空视图可以是字符串、视图或多个视图。
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f58f160594d21b1cf8826017f19ad31beb8eeda1
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77130986"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin. Forms CollectionView EmptyView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义以下属性，这些属性可用于在没有要显示的数据时提供用户反馈：

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)，类型 `object`、字符串、绑定或视图，将在 `null`[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性时或 `ItemsSource` 属性指定的集合 `null` 或为空时显示。 默认值是 `null`。
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，用于设置指定 `EmptyView`格式的模板。 默认值是 `null`。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

设置[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性的主要使用方案是当[`CollectionView`](xref:Xamarin.Forms.CollectionView)上的筛选操作不生成任何数据时显示用户反馈，并在从 web 服务检索数据时显示用户反馈。

> [!NOTE]
> 如果需要，可以将[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性设置为包含交互式内容的视图。

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>数据不可用时显示字符串

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性可以设置为字符串，该字符串将在[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性 `null`时显示，或者当 `ItemsSource` 属性指定的集合 `null` 或为空时显示。 以下 XAML 显示了此方案的示例：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

结果是，由于数据绑定集合是 `null`的，因此将显示设置为[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性值的字符串：

[![在 iOS 和 Android 上使用文本为空视图的 CollectionView 垂直列表屏幕截图](emptyview-images/null-itemssource.png "文本为空的 CollectionView 垂直列表视图")](emptyview-images/null-itemssource-large.png#lightbox "文本为空的 CollectionView 垂直列表视图")

## <a name="display-views-when-data-is-unavailable"></a>数据不可用时显示视图

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性可以设置为一个视图，该视图将在[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性 `null`时，或者 `ItemsSource` 属性指定的集合 `null` 或为空时显示。 这可以是单个视图，也可以是包含多个子视图的视图。 下面的 XAML 示例显示了设置为包含多个子视图的视图的 `EmptyView` 属性：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

等效 C# 代码如下：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

当[`SearchBar`](xref:Xamarin.Forms.SearchBar)执行 `FilterCommand`时，会针对存储在[`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text)属性中的搜索词筛选[`CollectionView`](xref:Xamarin.Forms.CollectionView)显示的集合。 如果筛选操作不产生任何数据，则显示[`StackLayout`](xref:Xamarin.Forms.StackLayout)设置为[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性值：

[![在 iOS 和 Android 上带有自定义空视图的 CollectionView 垂直列表屏幕截图](emptyview-images/filter-multiple-views.png "带有自定义空视图的 CollectionView 垂直列表")](emptyview-images/filter-multiple-views-large.png#lightbox "带有自定义空视图的 CollectionView 垂直列表")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>数据不可用时显示模板化自定义类型

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性可以设置为自定义类型，该类型的模板将在[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性 `null`时显示，或者当 `ItemsSource` 属性指定的集合 `null` 或为空时显示。 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)属性可以设置为定义 `EmptyView`外观的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 以下 XAML 显示了此方案的示例：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

等效 C# 代码如下：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

`FilterData` 类型定义 `Filter` 属性和相应的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)：

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性设置为 `FilterData` 对象，`Filter` 属性数据绑定到[`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text)属性。 当[`SearchBar`](xref:Xamarin.Forms.SearchBar)执行 `FilterCommand`时，会针对存储在 `Filter` 属性中的搜索词筛选[`CollectionView`](xref:Xamarin.Forms.CollectionView)显示的集合。 如果筛选操作不生成任何数据，则会显示在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中定义的[`Label`](xref:Xamarin.Forms.Label) ，并将其设置为[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)属性值：

[![在 iOS 和 Android 上带有空视图模板的 CollectionView 垂直列表屏幕截图](emptyview-images/emptyviewtemplate.png "带有空视图模板的 CollectionView 垂直列表")](emptyview-images/emptyviewtemplate-large.png#lightbox "带有空视图模板的 CollectionView 垂直列表")

> [!NOTE]
> 当数据不可用时显示模板化自定义类型时，可以将[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)属性设置为包含多个子视图的视图。

## <a name="choose-an-emptyview-at-runtime"></a>在运行时选择 EmptyView

当数据不可用时，将显示为[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)的视图可以定义为[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中[`ContentView`](xref:Xamarin.Forms.ContentView)对象。 然后，在运行时，可以根据某些业务逻辑将 `EmptyView` 属性设置为特定 `ContentView`。 下面的 XAML 示例显示了此方案的示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

此 XAML 定义了页面级[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中的两个[`ContentView`](xref:Xamarin.Forms.ContentView)对象，以及用于控制将设置为[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性值的 `ContentView` 对象的[`Switch`](xref:Xamarin.Forms.Switch)对象。 切换[`Switch`](xref:Xamarin.Forms.Switch)时，`OnEmptyViewSwitchToggled` 事件处理程序执行 `ToggleEmptyView` 方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

`ToggleEmptyView` 方法根据[`ResourceDictionary`](xref:Xamarin.Forms.Switch.IsToggled)属性的值将 `collectionView` 对象的[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性设置为存储在[`Switch.IsToggled`](xref:Xamarin.Forms.ResourceDictionary)中的两个[`ContentView`](xref:Xamarin.Forms.ContentView)对象之一。 当[`SearchBar`](xref:Xamarin.Forms.SearchBar)执行 `FilterCommand`时，会针对存储在[`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text)属性中的搜索词筛选[`CollectionView`](xref:Xamarin.Forms.CollectionView)显示的集合。 如果筛选操作不产生任何数据，则显示 `ContentView` 对象设置为 `EmptyView` 属性：

[![在 iOS 和 Android 上的 CollectionView 垂直列表中交换空视图的屏幕截图](emptyview-images/swap.png "带有交换空视图的 CollectionView 垂直列表")](emptyview-images/swap-large.png#lightbox "带有交换空视图的 CollectionView 垂直列表")

有关资源字典的详细信息，请参阅[Xamarin。 Forms 资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>在运行时选择 EmptyViewTemplate

可以在运行时根据其值选择[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)的外观，方法是将[`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)属性设置为[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)对象：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

等效 C# 代码如下：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性设置为[`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text)属性， [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)属性设置为 `SearchTermDataTemplateSelector` 对象。

当[`SearchBar`](xref:Xamarin.Forms.SearchBar)执行 `FilterCommand`时，会针对存储在[`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text)属性中的搜索词筛选[`CollectionView`](xref:Xamarin.Forms.CollectionView)显示的集合。 如果筛选操作不生成任何数据，则 `SearchTermDataTemplateSelector` 对象选择的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)将设置为[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)属性并显示。

下面的示例演示了 `SearchTermDataTemplateSelector` 类：

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

`SearchTermTemplateSelector` 类定义设置为不同数据模板 `DefaultTemplate` 和 `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)属性。 如果搜索查询不等于 "xamarin"，则 `OnSelectTemplate` 重写将返回 `DefaultTemplate`，这将向用户显示一条消息。 如果搜索查询等于 "xamarin"，则 `OnSelectTemplate` 重写将返回 `OtherTemplate`，这将向用户显示一条基本消息：

[![IOS 和 Android 上的 CollectionView 运行时空视图模板选择的屏幕截图](emptyview-images/datatemplateselector.png "CollectionView 中的运行时空视图模板选择")](emptyview-images/datatemplateselector-large.png#lightbox "CollectionView 中的运行时空视图模板选择")

有关数据模板选择器的详细信息，请参阅[Create a Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.Forms 资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
