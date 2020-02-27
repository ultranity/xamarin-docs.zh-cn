---
title: 资源字典
description: XAML 资源是可以共享和重复使用在 Xamarin.Forms 应用程序的对象。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2020
ms.custom: video
ms.openlocfilehash: f5266049e1498d902864185a61cba6d3730f9594
ms.sourcegitcommit: 2836f2003a5b745b042ee6003a3d6a11b9139e44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618865"
---
# <a name="resource-dictionaries"></a>资源字典

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_XAML 资源是可以在整个 Xamarin 应用程序中共享和重复使用的对象的定义。这些资源对象存储在资源字典中。_

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)是 Xamarin 应用程序使用的资源的存储库。 存储在 `ResourceDictionary` 中的典型资源包括[样式](~/xamarin-forms/user-interface/styles/index.md)、[控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)、[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、颜色和转换器。

在 XAML 中，可以通过使用 `StaticResource` 标记扩展来检索存储在 `ResourceDictionary` 中的资源，并将其应用于元素。 在C#中，还可以在 `ResourceDictionary` 中定义资源，然后使用基于字符串的索引器检索和应用到元素。 但是，在中C#使用 `ResourceDictionary` 没有什么优势，因为共享对象只可以存储为字段或属性，而无需首先从字典中检索它们。

## <a name="create-and-consume-a-resourcedictionary"></a>创建和使用 ResourceDictionary

在[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定义资源，然后将其设置为以下 `Resources` 属性之一：

- 派生自[`Application`](xref:Xamarin.Forms.Application)的任何类的[`Resources`](xref:Xamarin.Forms.Application.Resources)属性
- 派生自[`VisualElement`](xref:Xamarin.Forms.Application)的任何类的[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)属性

Xamarin 程序只包含一个派生自 `Application` 的类，但通常使用派生自 `VisualElement`的多个类，包括页面、布局和控件。 其中的任何对象都可以将其 `Resources` 属性设置为 `ResourceDictionary`。 选择在何处放置特定 `ResourceDictionary` 会影响资源的使用：

- 附加到视图的 `ResourceDictionary` 中的资源（如 `Button` 或 `Label`）只能应用于该特定对象，因此这并不是非常有用。
- 附加到布局的 `ResourceDictionary` 中的资源（如 `StackLayout` 或 `Grid`）可应用于布局和该布局的所有子级。
- 在页面级别定义的 `ResourceDictionary` 中的资源可以应用于页面及其所有子项。
- 在应用程序级别定义的 `ResourceDictionary` 中的资源可在整个应用程序中应用。

下面的 XAML 显示在应用程序级别 `ResourceDictionary` 中**定义的资源，该文件作为**标准 Xamarin. Forms 程序的一部分创建：

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

此 `ResourceDictionary` 定义了三个[`Color`](xref:Xamarin.Forms.Color)资源和一个[`Style`](xref:Xamarin.Forms.Style)资源。 有关 `App` 类的详细信息，请参阅[App class](~/xamarin-forms/app-fundamentals/application-class.md)。

从 Xamarin. Forms 3.0 开始，不需要显式 `ResourceDictionary` 标记。 自动创建 `ResourceDictionary` 对象，您可以在 `Resources` 属性元素标记之间直接插入资源：

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

每个资源都有一个使用 `x:Key` 特性指定的密钥，该属性将成为 `ResourceDictionary`中的字典键。 键用于从 `ResourceDictionary` 检索资源[`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)标记扩展，如以下 XAML 代码示例所示，其中显示了在 `StackLayout`中定义的附加资源：

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

第一个[`Label`](xref:Xamarin.Forms.Label)实例检索并使用应用程序级别 `ResourceDictionary`中定义的 `LabelPageHeadingStyle` 资源，第二个 `Label` 实例检索并使用控件级别 `LabelNormalStyle` 中定义的 `ResourceDictionary`资源。 同样， [`Button`](xref:Xamarin.Forms.Button)实例将检索和使用在应用程序级别中定义的 `NormalTextColor` 资源 `ResourceDictionary`，并使用在控件级别 `ResourceDictionary`定义的 `MediumBoldText` 资源。 这会导致如以下屏幕截图中所示的外观：

[![使用 ResourceDictionary 资源](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> 不应在应用程序级别的资源字典中，这种情况时所需的页面资源将随后在应用程序启动，而不是可以解析包含特定于单个页面的资源。 有关详细信息，请参阅[降低应用程序资源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="override-resources"></a>替代资源

当 `ResourceDictionary` 资源共享 `x:Key` 属性值时，视图层次结构中较低级别的资源将优先于定义较高的资源。 例如，在应用程序级别将 `PageBackgroundColor` 资源设置为 `Blue` 将被 `PageBackgroundColor` 资源集 `Yellow`的页级别重写。 同样，`PageBackgroundColor` 资源的页级别将被 `PageBackgroundColor` 资源的控件级别重写。 下面的 XAML 代码示例演示此优先顺序：

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

在应用程序级别定义的原始 `PageBackgroundColor` 和 `NormalTextColor` 实例由在页级定义的 `PageBackgroundColor` 和 `NormalTextColor` 实例重写。 因此，页面背景颜色变为蓝色，并且页面上的文本将变为黄色，如以下屏幕截图中所示：

[![重写 ResourceDictionary 资源](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

但请注意， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)的背景栏仍然是黄色的，因为[`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)属性设置为应用程序级别 `ResourceDictionary`中定义的 `PageBackgroundColor` 资源的值。

下面是考虑 `ResourceDictionary` 优先级的另一种方法：当 XAML 分析器遇到 `StaticResource`时，它通过使用它找到的第一个匹配项向上遍历可视化树来搜索匹配的键。 如果此搜索在页中结束，但仍未找到该键，则 XAML 分析器会搜索附加到 `App` 对象的 `ResourceDictionary`。 如果仍未找到该键，则引发异常。

## <a name="stand-alone-resource-dictionaries"></a>独立资源字典

派生自 `ResourceDictionary` 的类也可以在单独的独立文件中。 （更准确地说，派生自 `ResourceDictionary` 的类通常需要一_对_文件，因为资源是在 XAML 文件中定义的，但也需要具有 `InitializeComponent` 调用的代码隐藏文件。）然后，可以在应用程序之间共享结果文件。

若要创建此类文件，请向项目中添加新的**内容视图**或**内容页**项（但不包含只包含C#文件的**内容视图**或**内容页**）。 在 XAML 文件和C#文件中，将基类的名称从 "`ContentView`" 或 "`ContentPage`" 更改为 "`ResourceDictionary`"。 在 XAML 文件中，基类的名称是顶级元素。

下面的 XAML 示例显示了一个名为 `MyResourceDictionary`的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

此 `ResourceDictionary` 包含单个资源，这是一个 `DataTemplate`类型的对象。

可以通过在一对 `Resources` 属性元素标记（例如，在 `ContentPage`中）来实例化 `MyResourceDictionary`：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

`MyResourceDictionary` 的实例设置为 `ContentPage` 对象的 `Resources` 属性。

但是，这种方法存在一些限制： `ContentPage` 的 `Resources` 属性只引用此 `ResourceDictionary`。 在大多数情况下，您还希望选择包括其他 `ResourceDictionary` 实例，还可以包含其他资源。

此任务需要合并的资源字典。

## <a name="merged-resource-dictionaries"></a>合并资源字典

合并资源字典将一个或多个[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)对象合并到另一个 `ResourceDictionary`中。

### <a name="merge-local-resource-dictionaries"></a>合并本地资源字典

可以通过将[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)属性设置为包含以下资源的 XAML 文件的文件名，将本地[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)合并到另一个 `ResourceDictionary` 中：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <!-- Add more resources here -->
        <ResourceDictionary Source="MyResourceDictionary.xaml" />
        <!-- Add more resources here -->
    </ContentPage.Resources>
    ...
</ContentPage>
```

此语法不会实例化 `MyResourceDictionary` 类。 相反，它引用的 XAML 文件。 出于此原因，在设置[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)属性时，不需要代码隐藏文件（**MyResourceDictionary.xaml.cs**），并且可以从**MyResourceDictionary**文件的根标记中删除 `x:Class` 属性。 此外，使用此方法合并资源字典时，Xamarin 会自动实例化[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，因此不需要外部 `ResourceDictionary` 标记。

> [!IMPORTANT]
> 只能从 XAML 设置[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)属性。

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>从其他程序集合并资源字典

还可以通过将[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)添加到 `ResourceDictionary`的[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)属性中，将其合并到另一个 `ResourceDictionary` 中。 此方法允许合并资源字典，而不考虑它们所在的程序集。

> [!IMPORTANT]
> [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)类还定义了[`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith)属性。 但是，此属性已弃用，不应再使用。

下面的代码示例演示如何将 `MyResourceDictionary` 添加到页面级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)集合：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

此示例显示了一个实例，该 `MyResourceDictionary`实例驻留在要添加到[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的同一程序集中。 此外，还可以从其他程序集、 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)属性元素标记中的其他 `ResourceDictionary` 对象以及这些标记之外的其他资源添加资源字典：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

将[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)放置在外部程序集中时，请确保将其生成操作设置为**EmbeddedResource**。 此外，请确保它具有代码隐藏文件。

> [!IMPORTANT]
> [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中只能有一个 `MergedDictionaries` 属性元素标记，但是可以根据需要将任意数量的 `ResourceDictionary` 对象放入其中。

合并[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)资源 `x:Key` 属性值相同时，Xamarin 使用以下资源优先级：

1. 资源字典的本地资源中。
1. 资源字典中包含的资源，这些资源已通过 `MergedDictionaries` 集合合并，并按相反的顺序在 `MergedDictionaries` 属性中列出。

> [!NOTE]
> 搜索资源字典可以是计算密集型任务，如果应用程序包含多个大型资源字典。 因此，若要避免不需要的搜索，您应确保应用程序中的每一页仅使用适用于页的资源字典。

## <a name="related-links"></a>相关链接

- [资源字典（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
