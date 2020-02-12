---
title: Xamarin. Forms SearchBar
description: Xamarin SearchBar 是用于启动搜索的用户输入控件。 SearchBar 控件支持占位符文本、查询输入、执行和取消。 本文介绍如何使用 XAML 和代码中的 SearchBar。
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: a48a91b886cadcbe9dfa73a524b7bfa9fb2cf5fb
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77130977"
---
# <a name="xamarinforms-searchbar"></a>Xamarin. Forms SearchBar

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin [`SearchBar`](xref:Xamarin.Forms.SearchBar)是用于启动搜索的用户输入控件。 `SearchBar` 控件支持占位符文本、查询输入、搜索执行和取消。 以下屏幕截图显示了一个 `SearchBar` 查询，其中显示了 `ListView`中的结果：

[![IOS 和 Android 上 SearchBar 的屏幕截图](searchbar-images/device-searchbars-cropped.png "IOS 和 Android 上的 SearchBar")](searchbar-images/device-searchbars.png#lightbox "IOS 和 Android 上的 SearchBar")

`SearchBar` 类定义以下属性：

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)是定义 "取消" 按钮的颜色的 `Color`。
* `CharacterSpacing`，属于 `double` 类型，是 `SearchBar` 文本字符之间的间距。
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)是 `FontAttributes` 的枚举值，用于确定 `SearchBar` 字体是粗体、斜体还是两者皆无。
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)是确定 `SearchBar`使用的字体系列的 `string`。
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)可以是 `NamedSize` 枚举值或表示跨平台的特定字体大小的 `double` 值。
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)是定义查询文本的水平对齐方式的 `TextAlignment` 枚举值。
* `VerticalTextAlignment` 是定义查询文本的垂直对齐方式的 `TextAlignment` 枚举值。
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)是一种定义占位符文本的 `string`，如 "Search ..."。
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)是一种定义占位符文本颜色的 `Color`。
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)是一种允许将用户操作（如指指点击或单击）绑定到 viewmodel 上定义的命令的 `ICommand`。
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)是指定应传递到 `SearchCommand`的参数的 `object`。
* [`Text`](xref:Xamarin.Forms.InputView.Text)是包含 `SearchBar`中的查询文本的 `string`。
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)是一种定义查询文本颜色的 `Color`。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着 `SearchBar` 可自定义，并且是数据绑定的目标。 在 `SearchBar` 上指定字体属性与在其他 Xamarin 上自定义文本一致。[窗体文本控件](~/xamarin-forms/user-interface/text/index.md)。 有关详细信息，请参阅[Xamarin 中的字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="create-a-searchbar"></a>创建 SearchBar

可以在 XAML 中实例化 `SearchBar`。 可以设置其可选 `Placeholder` 属性，以便在查询输入框中定义提示文本。 `Placeholder` 的默认值为空字符串，因此如果未设置占位符，则不会出现占位符。 下面的示例演示如何使用可选的 `Placeholder` 属性集在 XAML 中实例化 `SearchBar`：

```xaml
<SearchBar Placeholder="Search items..." />
```

还可以在代码中创建 `SearchBar`：

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>SearchBar 外观属性

`SearchBar` 控件定义了许多自定义控件外观的属性。 下面的示例演示如何使用指定的多个属性在 XAML 中实例化 `SearchBar`：

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

在代码中创建 `SearchBar` 对象时，还可以指定这些属性：

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

以下屏幕截图显示了生成的 `SearchBar` 控件：

[![IOS 和 Android 上自定义 SearchBar 的屏幕截图](searchbar-images/device-searchbars-styled-cropped.png "IOS 和 Android 上的自定义 SearchBar")](searchbar-images/device-searchbars-styled.png#lightbox "IOS 和 Android 上的自定义 SearchBar")

> [!NOTE]
> 在 iOS 上，`SearchBarRenderer` 类包含可重写的 `UpdateCancelButton` 方法。 此方法控制 "取消" 按钮的显示时间，并可在自定义呈现器中被重写。 有关自定义呈现器的详细信息，请参阅[Xamarin。窗体自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

## <a name="perform-a-search-with-event-handlers"></a>使用事件处理程序执行搜索

可以通过将事件处理程序附加到以下事件之一，使用 `SearchBar` 控件执行搜索：

* 当用户单击 "搜索" 按钮或按 enter 键时，将调用[`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) 。
* 只要更改了查询框中的文本，就会调用[`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 。

下面的示例演示了一个附加到 XAML 中 `TextChanged` 事件的事件处理程序，并使用了 `ListView` 来显示搜索结果：

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

事件处理程序还可以附加到在代码中创建的 `SearchBar`：

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

无论是通过 XAML 还是代码创建 `SearchBar`，代码隐藏文件中的 `TextChanged` 事件处理程序都是相同的：

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

前面的示例说明存在 `DataService` 类，该方法具有能够返回与查询匹配的项的 `GetSearchResults` 方法。 `SearchBar` 控件的 `Text` 属性值将传递给 `GetSearchResults` 方法，结果用于更新 `ListView` 控件的 `ItemsSource` 属性。 整体效果是在 `ListView` 控件中显示搜索结果。

示例应用程序提供了一个可用于测试搜索功能的 `DataService` 类实现。

## <a name="perform-a-search-using-a-viewmodel"></a>使用 viewmodel 执行搜索

可以在没有事件处理程序的情况下执行搜索，方法是将 `SearchCommand` 和 `SearchCommandParameter` 属性绑定到 `ICommand` 实现。 示例项目使用模型-视图-ViewModel （MVVM）模式演示这些实现。 有关与 MVVM 的数据绑定的详细信息，请参阅[通过 mvvm 进行数据绑定](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

示例应用程序中的 viewmodel 包含以下代码：

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> Viewmodel 假设存在能够执行搜索的 `DataService` 类。 示例应用程序中提供了 `DataService` 类（包括示例数据）。

以下 XAML 演示了如何使用显示搜索结果的 `ListView` 控件将 `SearchBar` 绑定到示例 viewmodel：

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

此示例将 `BindingContext` 设置为 `SearchViewModel` 类的实例。 它将 `SearchCommand` 属性绑定到 viewmodel 中的 `PerformSearch` `ICommand`，并将 `SearchBar` `Text` 属性绑定到 `SearchCommandParameter` 属性。 `ListView.ItemsSource` 属性绑定到 viewmodel 的 `SearchResults` 属性。

有关 `ICommand` 接口和绑定的详细信息，请参阅[Xamarin。窗体数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)和[ICommand 接口](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="related-links"></a>相关链接

* [SearchBar 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin. Forms 文本控件](~/xamarin-forms/user-interface/text/index.md)
* [Xamarin 中的字体](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin. 窗体数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
