---
title: 在 Xamarin 中的运行时加载 XAML
description: 可以在运行时通过 LoadFromXaml 扩展方法加载和分析 XAML。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: 71f510cd37d4bed2a5a6077ed63f748ce9894725
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045073"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>在 Xamarin 中的运行时加载 XAML

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

[`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml)命名空间包括两个可用于在运行时加载和分析 XAML 的[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)扩展方法。

## <a name="background"></a>背景

构造 Xamarin. Forms XAML 类时，将间接调用[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法。 之所以发生这种情况，是因为 XAML 类的代码隐藏文件从其构造函数中调用 `InitializeComponent` 方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

当 Visual Studio 生成包含 XAML 文件的项目时，它会分析 XAML 文件以生成包含C# `InitializeComponent` 方法的定义的代码文件（例如， **MainPage.xaml.g.cs**）：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent` 方法调用[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法从 .NET Standard 库中提取 XAML 文件（或其已编译的二进制文件）。 提取后，它将初始化在 XAML 文件中定义的所有对象，将它们一起连接到父-子关系，将代码中定义的事件处理程序附加到 XAML 文件中设置的事件，并将对象的结果树设置为分页.

## <a name="loading-xaml-at-runtime"></a>在运行时加载 XAML

`public`[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法，因此可以从 Xamarin 应用程序中调用，以便在运行时加载和分析 XAML。 这将允许诸如应用程序从 web 服务下载 XAML、从 XAML 创建所需的视图，以及在应用程序中显示该视图之类的方案。

> [!WARNING]
> 在运行时加载 XAML 会产生极高的性能开销，通常应避免这样做。

下面的代码示例演示了一个简单的用法：

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

在此示例中，创建了一个[`Button`](xref:Xamarin.Forms.Button)实例，其[`Text`](xref:Xamarin.Forms.Button.Text)属性值是从 `string`中定义的 XAML 设置的。 然后，将 `Button` 添加到在该页面的 XAML 中定义的[`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

> [!NOTE]
> [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)扩展方法允许指定泛型类型参数。 但是，很少需要指定类型参数，因为它将从它所操作的实例的类型推断。

[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可用于处理任意 XAML，下面的示例因为这样做一个[`ContentPage`](xref:Xamarin.Forms.ContentPage)然后导航到该方法：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>访问元素

使用[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法在运行时加载 xaml 不允许强类型访问具有指定运行时对象名称（使用 `x:Name`）的 xaml 元素。 但是，可以使用[`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法检索这些 XAML 元素，然后根据需要进行访问：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此示例中， [`ContentPage`](xref:Xamarin.Forms.ContentPage)的 XAML 被放大。 此 XAML 包含一个名为 `monkeyName`的[`Label`](xref:Xamarin.Forms.Label) ，它是在设置其[`Text`](xref:Xamarin.Forms.Label.Text)属性之前使用[`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法检索的。

## <a name="related-links"></a>相关链接

- [LoadRuntimeXAML （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
