---
title: ListView 性能
description: 尽管 ListView 是功能强大的视图，以显示数据，但它具有一些限制。 本文介绍如何确保与应用程序中的 Xamarin.Forms ListView 优异的性能。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916960"
---
# <a name="listview-performance"></a>ListView 性能

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

如果编写移动应用程序，性能很重要。 用户都希望能平滑滚动并快速加载。 未能满足用户的期望将您的应用程序商店中的评级或成本对于业务线应用程序，成本的组织的时间和金钱。

Xamarin [`ListView`](xref:Xamarin.Forms.ListView)是用于显示数据的功能强大的视图，但它存在一些限制。 使用自定义单元时，尤其是当它们包含深层嵌套的视图层次结构或使用某些需要复杂度量的布局时，滚动性能可能会受到影响。 幸运的是，有可用于避免性能不佳的技术。

## <a name="caching-strategy"></a>缓存策略

Listview 通常用于显示比屏幕容纳更多的数据。 例如，音乐应用可能有一个歌曲库，其中包含上千个条目。 为每个条目创建一项会浪费宝贵的内存，并且性能会下降。 经常创建和销毁行需要应用程序不断地实例化和清理对象，这也会导致性能下降。

为了节省内存，每个平台的本机[`ListView`](xref:Xamarin.Forms.ListView)等效项具有用于重用行的内置功能。 只有屏幕上可见的单元格才会加载到内存中，并且**内容**将加载到现有单元中。 此模式可防止应用程序实例化数千个对象，从而节省了时间和内存。

Xamarin 通过[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)枚举允许[`ListView`](xref:Xamarin.Forms.ListView)单元格重用，此枚举具有以下值：

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平台（UWP）会忽略[`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略，因为它始终使用缓存来提高性能。 因此，默认情况下，它的行为如同应用[`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略一样。

### <a name="retainelement"></a>RetainElement

[`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略指定[`ListView`](xref:Xamarin.Forms.ListView)将为列表中的每一项生成一个单元格，并且为默认的 `ListView` 行为。 应在以下情况下使用它：

- 每个单元格有大量绑定（20-30 +）。
- 单元模板会频繁更改。
- 测试显示 `RecycleElement` 缓存策略会导致执行速度降低。

使用自定义单元时，务必识别[`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略的后果。 任何单元格初始化代码将需要为每个单元格创建过程中，运行的可为每秒多次。 在这种情况下，在页面上正常运行的布局技术（如使用多个嵌套[`StackLayout`](xref:Xamarin.Forms.StackLayout)实例）在用户滚动时被实时设置并销毁时会成为性能瓶颈。

### <a name="recycleelement"></a>RecycleElement

[`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略指定[`ListView`](xref:Xamarin.Forms.ListView)将通过回收列表单元来尝试最大程度地减少内存占用量和执行速度。 此模式并非始终能改善性能，并应执行测试来确定是否有任何改进。 但这是首选选项，应在以下情况下使用：

- 每个单元格的绑定数小到中等。
- 每个单元格的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)都定义了所有单元数据。
- 每个单元格在很大程度上相似，单元模板不变。

在虚拟化期间，该单元格都有更新，其绑定上下文，因此如果应用程序使用此模式下则必须确保适当地处理绑定上下文更新。 有关单元格的所有数据必须都来自绑定上下文或可能会出现一致性错误。 可以通过使用数据绑定显示单元数据来避免此问题。 此外，单元数据应在 `OnBindingContextChanged` 重写中设置，而不是在自定义单元的构造函数中设置，如下面的代码示例所示：

```csharp
public class CustomCell : ViewCell
{
    Image image = null;
    
    public CustomCell ()
    {
        image = new Image();
        View = image;
    }
    
    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();
        
        var item = BindingContext as ImageItem;
        if (item != null) {
            image.Source = item.ImageUrl;
        }
    }
}
```

有关详细信息，请参阅[绑定上下文更改](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果单元格使用自定义呈现器，它们必须确保正确实现属性更改通知。 当重复使用单元格时，将在绑定上下文更新到可用单元格时更改其属性值，并引发 `PropertyChanged` 事件。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>使用 DataTemplateSelector RecycleElement

当[`ListView`](xref:Xamarin.Forms.ListView)使用[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)时， [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略不会缓存 `DataTemplate`。 相反，将为列表中的每个数据项选择一个 `DataTemplate`。

> [!NOTE]
> [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略具有 Xamarin. Forms 2.4 中引入的先决条件，当要求[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)选择每个 `DataTemplate` 必须返回相同[`ViewCell`](xref:Xamarin.Forms.ViewCell)类型的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)时。 例如，如果给定[`ListView`](xref:Xamarin.Forms.ListView)的 `DataTemplateSelector` 可以返回 `MyDataTemplateA` （其中 `MyDataTemplateA` 返回 `ViewCell` 类型 `MyViewCellA`）或 `MyDataTemplateB` （其中 `MyDataTemplateB` 返回类型 `ViewCell` `MyViewCellB`），则在返回 `MyDataTemplateA` 时，它必须返回 `MyViewCellA`，否则将引发异常。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)缓存策略基于[`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略建立，另外还要确保当[`ListView`](xref:Xamarin.Forms.ListView)使用[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)来选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)时，列表中的项的类型将缓存 `DataTemplate`。 因此，将为每个项目类型选择一次 `DataTemplate`，而不是对每个项目实例进行一次选择。

> [!NOTE]
> [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)缓存策略具有先决条件， [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)返回的 `DataTemplate`必须使用采用 `Type`的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))构造函数。

### <a name="set-the-caching-strategy"></a>设置缓存策略

使用[`ListView`](xref:Xamarin.Forms.ListView)构造函数重载指定[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)枚举值，如下面的代码示例所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 XAML 中，按下面的 XAML 中所示设置 `CachingStrategy` 特性：

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

此方法与在C#的构造函数中设置缓存策略参数的效果相同。

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>在子类 ListView 中设置缓存策略

在子类[`ListView`](xref:Xamarin.Forms.ListView)上设置 XAML 中的 `CachingStrategy` 属性将不会生成所需的行为，因为 `ListView`上没有 `CachingStrategy` 属性。 此外，如果启用了[XAMLC](~/xamarin-forms/xaml/xamlc.md) ，则将生成以下错误消息：**没有为 "CachingStrategy" 找到属性、可绑定属性或事件**

此问题的解决方法是在子类[`ListView`](xref:Xamarin.Forms.ListView)上指定接受[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)参数的构造函数，并将其传递到基类：

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

然后，可以通过使用 `x:Arguments` 语法从 XAML 指定[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)枚举值：

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>ListView 性能建议

有多种方法可以提高 `ListView`的性能。 以下建议可以提高 ListView 的性能

- 将 `ItemsSource` 属性绑定到 `IList<T>` 集合而不是 `IEnumerable<T>` 集合，因为 `IEnumerable<T>` 集合不支持随机访问。
- 无论何时，都可以使用内置单元（如 `TextCell` / `SwitchCell`）而不是 `ViewCell`。
- 使用更少元素。 例如，请考虑使用单个 `FormattedString` 标签而不是多个标签。
- 显示非同源数据时将 `ListView` 替换为 `TableView`，即不同类型的数据。
- 限制[`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法的使用。 如果过度使用，它会降低性能。
- 在 Android 上，请避免在实例化后设置 `ListView`的行分隔符可见性或颜色，因为这样会导致性能大幅下降。
- 避免根据[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)更改单元格布局。 更改布局会产生大量的度量和初始化成本。
- 避免将深度嵌套的布局层次结构。 使用 `AbsoluteLayout` 或 `Grid` 来帮助减少嵌套。
- 避免 `Fill` 以外的特定 `LayoutOptions` （`Fill` 是计算成本的最便宜）。
- 出于以下原因，请避免在 `ScrollView` 中放置 `ListView`：
  - `ListView` 实现其自己的滚动。
  - `ListView` 将不会接收任何手势，因为父 `ScrollView`会处理它们。
  - `ListView` 可以显示自定义的标头和脚注，该标头和表尾滚动列表的元素，可能提供 `ScrollView` 所用的功能。 有关详细信息，请参阅[页眉和页脚](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)。
- 如果你需要在单元格中提供特定的复杂设计，则请考虑使用自定义呈现器。

`AbsoluteLayout` 有可能在没有单个度量值调用的情况下执行布局，使其具有高性能。 如果 `AbsoluteLayout` 无法使用，请考虑[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)。 如果使用 `RelativeLayout`，则直接传递约束比使用表达式 API 快得多。 此方法的速度更快，因为表达式 API 使用 JIT，而在 iOS 上必须解释此树，这会降低。 表达式 API 适用于仅在初始布局和旋转时需要的页面布局，但在 `ListView`中，它会在滚动过程中持续运行，从而影响性能。

为[`ListView`](xref:Xamarin.Forms.ListView)或其单元生成自定义呈现器是降低布局计算对滚动性能的影响的一种方法。 有关详细信息，请参阅[自定义 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)和[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

## <a name="related-links"></a>相关链接

- [自定义呈现器视图（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自定义呈现器 ViewCell （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
