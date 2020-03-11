---
title: 在 Xamarin.Forms 中的动态样式
description: 本文介绍如何 Xamarin.Forms 应用程序可以响应在运行时动态样式更改通过使用动态资源。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918642"
---
# <a name="dynamic-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的动态样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_样式不会对属性更改做出响应，并在应用程序持续时间内保持不变。例如，在将样式分配给视觉对象后，如果修改、删除或添加了新的 Setter 实例，则不会将更改应用于可视元素。但是，应用程序可以使用动态资源在运行时动态响应样式更改。_

`DynamicResource` 标记扩展类似于 `StaticResource` 标记扩展，因为这两个标记都使用字典键从[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)获取值。 但是，虽然 `StaticResource` 执行单个字典查找，但 `DynamicResource` 会保留一个指向字典键的链接。 因此，如果替换与键关联的字典条目，更改将应用到可视元素。 这样，在应用程序中进行的运行时样式更改。

下面的代码示例演示了 XAML 页中的*动态*样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar)实例使用 `DynamicResource` 标记扩展来引用未在 XAML 中定义的名为 `searchBarStyle`的[`Style`](xref:Xamarin.Forms.Style) 。 但是，因为 `SearchBar` 实例的[`Style`](xref:Xamarin.Forms.NavigableElement.Style)属性是使用 `DynamicResource`设置的，缺少的字典键不会导致引发异常。

相反，在代码隐藏文件中，构造函数创建一个包含键 `searchBarStyle`[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)项，如下面的代码示例所示：

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

执行 `OnButtonClicked` 事件处理程序时，`searchBarStyle` 将在 `blueSearchBarStyle` 和 `greenSearchBarStyle`之间切换。 这会导致如以下屏幕截图中所示的外观：

[![蓝色动态样式示例](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![绿色动态样式示例](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

下面的代码示例演示如何在 C# 中的等效页：

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

在C#中， [`SearchBar`](xref:Xamarin.Forms.SearchBar)实例使用[`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*)方法来引用 `searchBarStyle`。 `OnButtonClicked` 事件处理程序代码与 XAML 示例相同，并且在执行时，`searchBarStyle` 将在 `blueSearchBarStyle` 和 `greenSearchBarStyle`之间切换。

## <a name="dynamic-style-inheritance"></a>动态样式继承

无法使用[`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn)属性从动态样式中派生样式。 相反， [`Style`](xref:Xamarin.Forms.Style)类包括[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)属性，该属性可设置为其值可能会动态更改的字典键。

下面的代码示例演示了 XAML 页中的*动态*样式继承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar)实例使用 `StaticResource` 标记扩展来引用名为 `tealSearchBarStyle`的[`Style`](xref:Xamarin.Forms.Style) 。 此 `Style` 设置一些附加属性，并使用[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)属性 `searchBarStyle`引用。 `DynamicResource` 标记扩展不是必需的，因为 `tealSearchBarStyle` 将不会更改，但它派生自的 `Style` 除外。 因此，`tealSearchBarStyle` 维护一个指向 `searchBarStyle` 的链接，并在基本样式发生更改时进行更改。

在代码隐藏文件中，构造函数创建一个包含键 `searchBarStyle`的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)条目，如上面演示动态样式的示例所示。 执行 `OnButtonClicked` 事件处理程序时，`searchBarStyle` 将在 `blueSearchBarStyle` 和 `greenSearchBarStyle`之间切换。 这会导致如以下屏幕截图中所示的外观：

[![蓝色动态样式继承示例](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![绿色动态样式继承示例](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

下面的代码示例演示如何在 C# 中的等效页：

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

`tealSearchBarStyle` 将直接分配给[`SearchBar`](xref:Xamarin.Forms.SearchBar)实例的[`Style`](xref:Xamarin.Forms.NavigableElement.Style)属性。 此 `Style` 设置一些附加属性，并使用[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)属性引用 `searchBarStyle`。 此处不需要[`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*)方法，因为 `tealSearchBarStyle` 将不会更改，但它派生自的 `Style` 除外。 因此，`tealSearchBarStyle` 维护一个指向 `searchBarStyle` 的链接，并在基本样式发生更改时进行更改。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [动态样式（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [使用样式（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
