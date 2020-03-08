---
title: 提高 Xamarin.Forms 应用性能
description: 可以通过许多方法来提高使用 Xamarin.Forms 应用程序的性能。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
ms.openlocfilehash: 4427d347723284a2f8897612f10857270c9631bf
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78913411"
---
# <a name="improve-xamarinforms-app-performance"></a>提高 Xamarin.Forms 应用性能

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016：使用 Xamarin.Forms 优化应用性能**

应用程序性能差表现在许多方面。 这会使应用程序看起来无响应，导致滚动缓慢，还可降低设备电池寿命。 但是，优化性能不止需要实现高效的代码。 还必须考虑用户对应用程序性能的体验。 例如，确保操作执行不会妨碍用户执行其他活动，这有助于改进用户的体验。

可以通过许多方法来提高 Xamarin.Forms 应用程序的性能和感知性能。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。

> [!NOTE]
> 阅读本文之前，首先应阅读[跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中讨论了非平台特定方法，可用于改善使用 Xamarin 平台生成的应用程序的内存使用情况和性能。

## <a name="enable-the-xaml-compiler"></a>启用 XAML 编译器

XAML 可以根据需要使用 XAML 编译器 (XAMLC) 直接编译为中间语言 (IL)。 XAMLC 提供了一些好处：

- 它会执行 XAML 的编译时检查，从而可向用户通知任何错误。
- 它会消除 XAML 元素的某些负载和实例化时间。
- 它通过不再包含 .xaml 文件，来帮助减小最终程序集的文件大小。

默认情况下在新的 Xamarin.Forms 解决方案中启用 XAMLC。 但是，还可能需要在旧版的解决方案中启用它。 有关详细信息，请参阅[编译 XAML](~/xamarin-forms/xaml/xamlc.md)。

## <a name="use-compiled-bindings"></a>使用已编译的绑定

已编译的绑定通过在编译时解析绑定表达式而不是在运行时使用反射来提升 Xamarin.Forms 应用程序中的数据绑定性能。 编译绑定表达式会生成编译代码，通常比使用经典绑定快 8 到 20 倍。 有关详细信息，请参阅[已编译的绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="reduce-unnecessary-bindings"></a>减少不需要的绑定

不要将绑定用于可以方便地进行静态设置的内容。 绑定无需绑定的数据不会带来优势，因为绑定并不经济高效。 例如，设置 `Button.Text = "Accept"` 的开销要低于将 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 绑定到值为“Accept”值的 viewmodel `string` 属性。

## <a name="use-fast-renderers"></a>使用快速呈现器

快速呈现器让生成的原生控件层次结构平展化，减少了 Android 上 Xamarin.Forms 控件的通货膨胀和呈现成本。 这样一来，创建的对象就变少了，相应地也降低了可视化树的复杂性和内存使用率，从而进一步提升了性能。

从 Xamarin.Forms 4.0 开始，所有以 `FormsAppCompatActivity` 为目标的应用程序均默认使用快速呈现器。 有关详细信息，请参阅[快速呈现器](~/xamarin-forms/internals/fast-renderers.md)。

## <a name="enable-startup-tracing-on-android"></a>在 Android 上启用启动跟踪

在 Android 上预先编译 (AOT) 可最小化实时 (JIT) 应用程序启动开销和内存使用情况，代价是会创建更大的 APK。 一种替代方法是使用启动跟踪，与传统的 AOT 编译相比，启动跟踪在 Android APK 大小和启动时间之间可实现权衡。

与尽可能多地将应用程序编译为非托管代码不同，启动跟踪只编译一组托管方法，这些方法表示空白 Xamarin.Forms 应用程序中最昂贵的应用程序启动部分。 与传统的 AOT 编译相比，这种方法可以减少 APK 的大小，同时仍然提供类似的启动改进。

## <a name="enable-layout-compression"></a>启用布局压缩

为了提升页面呈现性能，布局压缩从可视化树中删除指定的布局。 这带来的性能优势因页面复杂性、要使用的操作系统版本以及运行应用的设备而异。 不过，在旧设备上实现的性能提升最大。 有关详细信息，请参阅[布局压缩](~/xamarin-forms/user-interface/layouts/layout-compression.md)。

## <a name="choose-the-correct-layout"></a>选择正确布局

能够显示多个子级，但只具有单个子级的布局会比较浪费。 例如，下面的代码示例演示一个具有单个子级的 [`StackLayout`](xref:Xamarin.Forms.StackLayout)：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

这比较浪费，应删除 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 元素，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

此外，不要尝试使用其他布局的组合来重现特定布局的外观，因为这会导致执行不需要的布局计算。 例如，不要尝试使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 实例的组合来重现 [`Grid`](xref:Xamarin.Forms.Grid) 布局。 下面的代码示例演示了这种错误做法的示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

这比较浪费，因为会执行不需要的布局计算。 相反，可以使用 [`Grid`](xref:Xamarin.Forms.Grid) 更好地实现所需布局，如下面的代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="100" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Text="Name:" />
        <Entry Grid.Column="1" Placeholder="Enter your name" />
        <Label Grid.Row="1" Text="Age:" />
        <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
        <Label Grid.Row="2" Text="Occupation:" />
        <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
        <Label Grid.Row="3" Text="Address:" />
        <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
    </Grid>
</ContentPage>
```

## <a name="optimize-layout-performance"></a>优化布局性能

若要获取最佳可能布局性能，请遵循以下准则：

- 通过指定 [`Margin`](xref:Xamarin.Forms.View.Margin) 属性值来减少布局层次结构的深度，从而允许创建具有更少换行视图的布局。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。
- 使用 [`Grid`](xref:Xamarin.Forms.Grid) 时，尝试确保将尽可能少的行和列设置为 [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小。 每个自动调整大小的行或列都会导致布局引擎执行额外布局计算。 而是应在可能时使用固定大小的行和列。 或者，使用 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) 枚举值将行和列设置，为占据成比例的空间量，前提是父树遵循这些布局准则。
- 除非需要，否则不要设置布局的 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 和 [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性。 [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 和 [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 的默认值可以实现最佳布局优化。 更改这些属性会产生成本并消耗内存，即使是将它们设置为默认值。
- 尽可能避免使用 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)。 它会导致 CPU 不得不执行显著更多的工作。
- 使用 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 时，尽可能避免使用 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 属性。
- 使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 时，确保只有一个子级设置为 [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)。 此属性可确保指定子级会占用 `StackLayout` 可以向它提供的最大空间，而多次执行这些计算比较浪费。
- 避免调用 [`Layout`](xref:Xamarin.Forms.Layout) 类的任何方法，因为它们会导致执行成本高昂的布局计算。 相反，可能可以通过设置 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 属性来获取所需布局行为。 或者，将 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 类设为子类以实现所需布局行为。
- 不要比需要更频繁地更新任何 [`Label`](xref:Xamarin.Forms.Label) 实例，因为标签大小的更改可能会导致重新计算整个屏幕布局。
- 除非需要，否则不要设置 [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) 属性。
- 尽可能将任何 [`Label`](xref:Xamarin.Forms.Label) 实例的 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 都设置为 [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap)。

## <a name="use-asynchronous-programming"></a>使用异步编程

通过使用异步编程，可增强应用程序的总体响应能力，而且通常可避免性能瓶颈。 在 .NET 中，[基于任务的异步模式 (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) 是异步操作的推荐设计模式。 但是，如果 TAP 使用不当，则可能会导致应用程序性能不佳。 因此，在使用 TAP 时，应遵循以下准则。

### <a name="fundamentals"></a>基础知识

- 了解由 `TaskStatus` 枚举表示的任务生命周期。 有关详细信息，请参阅 [TaskStatus 的含义](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/)和[任务状态](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status)。
- 使用 `Task.WhenAll` 方法异步地等待多个异步操作完成，而不是使用 `await` 单独等待一系列异步操作。 有关详细信息，请参阅 [Task.WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall)。
- 使用 `Task.WhenAny` 方法异步地等待多个异步操作中的一个操作完成。 有关详细信息，请参阅 [Task.WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall)。
- 使用 `Task.Delay` 方法生成在指定时间后完成的 `Task` 对象。 对于轮询数据和将用户输入的处理延迟预定时间之类的场景，这非常有用。 有关详细信息，请参阅 [Task.Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay)。
- 使用 `Task.Run` 方法对线程池执行密集型同步 CPU 操作。 此方法是 `TaskFactory.StartNew` 方法的快捷方式，其中设置的参数最佳。 有关详细信息，请参阅 [Task.Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun)。
- 避免尝试创建异步构造函数。 请改用生命周期事件或单独的初始化逻辑，以使用 `await` 正确处理任何初始化。 有关详细信息，请参阅 blog.stephencleary.com 上的 [Async 构造函数](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html)。
- 使用惰性任务模式，避免在应用程序启动过程中等待异步操作完成。 有关详细信息，请参阅 [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/)。
- 通过创建 `TaskCompletionSource<T>` 对象，为不使用 TAP 的现有异步操作创建任务包装器。 这些对象获得 `Task` 可编程性的优点，并使你能够控制关联 `Task` 的生存期和完成。 有关详细信息，请参阅 [TaskCompletionSource 的性质](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/)。
 
- 当无需处理异步操作的结果时，返回 `Task` 对象，而不是返回等待的 `Task` 对象。 由于执行的上下文切换较少，因此性能更高。
- 在数据可用时处理数据，或在你有多个必须以异步方式彼此通信的操作等场景下，使用任务并行库 (TPL) 数据流库。 有关详细信息，请参阅[数据流（任务并行库）](/dotnet/standard/parallel-programming/dataflow-task-parallel-library)。

### <a name="ui"></a>UI

- 调用 API 的异步版本（若可用）。 这将保持 UI 线程通畅，从而有助于提升用户对应用程序的体验。
- 使用 UI 线程上异步操作中的数据更新 UI 元素，以避免引发异常。 但是，对 `ListView.ItemsSource` 属性的更新将自动被封送到该 UI 线程。 要了解如何确定代码是否在 UI 线程上运行，请参阅 [Xamarin.Essentials:MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms)。

    > [!IMPORTANT]
    > 通过数据绑定更新的控件属性都将自动被封送到该 UI 线程。

### <a name="error-handling"></a>错误处理

- 了解异步异常处理。 由异步运行的用户代码引发的未处理异常会传播回调用线程（某些情况除外）。 有关详细信息，请参阅[异常处理（任务并行库）](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library)。
- 不要创建 `async void` 方法，而是创建 `async Task` 方法。 这些方法更便于实现错误处理、可组合性和可测试性。 此指导原则的例外情况是异步事件处理程序，这类处理程序必须返回 `void`。 有关详细信息，请参阅[避免 Async Void](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void)。
- 请勿通过调用 `Task.Wait`、`Task.Result` 或 `GetAwaiter().GetResult` 方法将阻止代码和异步代码混杂在一起，因为它们会导致死锁的发生。 但是，如果必须违反此准则，则首选的方法是调用 `GetAwaiter().GetResult` 方法，因为它将保留任务异常。 有关详细信息，请参阅[始终使用 Async](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) 和 [.NET 4.5 中的任务异常处理](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/)。
- 尽可能使用 `ConfigureAwait` 方法创建无上下文的代码。 无上下文的代码对于移动应用程序而言性能更佳具，是一种可在使用部分异步代码库时避免死锁的方法。 有关详细信息，请参阅[配置上下文](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context)。
- 使用“延续任务”来实现一些功能，例如处理上一个异步操作引发的异常以及在延续开始前或运行时取消延续  。 有关详细信息，请参阅[使用延续任务链接任务](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks)。
- 从 `ICommand` 中调用异步操作时，使用异步 `ICommand` 实现。 这确保了异步命令逻辑中的任何异常都可得到处理。 有关详细信息，请参阅[异步编程：针对异步 MVVM 应用程序的模式：命令](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands)。

## <a name="choose-a-dependency-injection-container-carefully"></a>仔细选择依赖关系注入容器

依赖关系注入容器在移动应用程序中引入了其他性能限制。 使用容器来注册和解析类型会影响性能，因为容器使用反射来创建每个类型，特别是在应用中为每个页面导航重构依赖关系的情况。 如果存在许多或深度依赖关系，则创建成本会显著增加。 此外，类型注册（通常在应用程序启动期间发生）可能会对启动时间产生明显影响，具体取决于所使用的容器。

作为替代方案，通过使用工厂手动实现依赖关系注入，可以使依赖关系注入更加高效。

## <a name="create-shell-applications"></a>创建 Shell 应用程序

Xamarin.Forms Shell 应用程序提供了基于浮出控件和选项卡的固定导航体验。 如果应用程序用户体验可以通过 Shell 实现，那么这样做很有用。 Shell 应用程序有助于避免糟糕的启动体验，因为页面是根据导航所需而创建的，而不是在应用程序启动时创建，而在使用 [`TabbedPage'](xref:Xamarin.Forms.TabbedPage) 的应用程序中会出现这种情况。 有关详细信息，请参阅 [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="use-collectionview-instead-of-listview"></a>使用 CollectionView 而不是 ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同布局规范显示数据列表的视图。 它为 [`ListView`](xref:Xamarin.Forms.ListView) 提供了更灵活、更高效的替代方案。 有关详细信息，请参阅 [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="optimize-listview-performance"></a>优化 ListView 性能

使用 [`ListView`](xref:Xamarin.Forms.ListView) 时，应对许多用户体验进行优化：

- **初始化** – 从创建控件时开始，到在屏幕上显示项时结束的时间间隔。
- **滚动** – 能够滚动列表，并确保 UI 不滞后于触控笔势。
- **交互**，用于添加、删除和选择项。

[`ListView`](xref:Xamarin.Forms.ListView) 控件需要应用程序提供数据和单元格模板。 实现此目标的方法会对该控件的性能产生很大影响。 有关详细信息，请参阅 [ListView 性能](~/xamarin-forms/user-interface/listview/performance.md)。

## <a name="optimize-image-resources"></a>优化图像资源

显示图像资源可能会极大提高应用程序的内存占用情况。 因此，仅应在必要时创建图像，应用程序不再需要图像后应立即将其释放。 例如，如果应用程序通过从流中读取其数据来显示图像，请确保仅当需要时才创建流，并确保在不再需要时释放流。 可以通过在创建页面时或是在 [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) 事件触发时创建流，然后在 [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) 事件触发时释放流，来实现此目标。

使用 [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) 方法下载图像进行显示时，通过确保将 [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) 属性设置为 `true`，来缓存下载的图像。 有关详细信息，请参阅[使用图像](~/xamarin-forms/user-interface/images.md)。

有关详细信息，请参阅[优化图像资源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

## <a name="reduce-the-visual-tree-size"></a>减小可视化树大小

减少页面上的元素数可以更快呈现页面。 可通过两种主要方法来实现此目标。 第一种方法是隐藏不可见的元素。 每个元素的 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 属性可确定该元素是否应属于可视化树的一部分。 因此，如果某个元素因为隐藏在其他元素后面而不可见，则删除该元素，或将其 `IsVisible` 属性设置为 `false`。

第二种方法是删除不需要的元素。 例如，以下代码示例显示包含多个 [`Label`](xref:Xamarin.Forms.Label) 对象的页面布局：

```xaml
<StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Hello" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Welcome to the App!" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Downloading Data..." />
    </StackLayout>
</StackLayout>
```

可以使用减少的元素数来维持相同的页面布局，如下面的代码示例所示：

```xaml
<StackLayout Padding="20,35,20,20" Spacing="25">
  <Label Text="Hello" />
  <Label Text="Welcome to the App!" />
  <Label Text="Downloading Data..." />
</StackLayout>
```

## <a name="reduce-the-application-resource-dictionary-size"></a>减小应用程序资源字典大小

在整个应用程序中使用的任何资源都应存储在应用程序的资源字典中以避免重复。 这会有助于减少整个应用程序中必须进行分析的 XAML 量。 下面的代码示例演示 `HeadingLabelStyle` 资源，它在应用程序范围内使用，因此在应用程序的资源字典中进行定义：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
         <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
         </ResourceDictionary>
     </Application.Resources>
</Application>
```

但是，特定于页面的 XAML 不应包含在应用程序的资源字典中，因为这些资源随后会在应用程序启动时（而不是页面需要时）进行分析。 如果某个资源由不是启动页面的页面使用，则它应置于该页面的资源字典中，因此有助于减少在应用程序启动时分析的 XAML。 下面的代码示例演示 `HeadingLabelStyle` 资源，它只位于单个页面上，因此在该页面的资源字典中进行定义：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Resources>
        <ResourceDictionary>
          <Style x:Key="HeadingLabelStyle" TargetType="Label">
              <Setter Property="HorizontalOptions" Value="Center" />
              <Setter Property="FontSize" Value="Large" />
              <Setter Property="TextColor" Value="Red" />
          </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

有关应用程序资源的详细信息，请参阅 [XAML 样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

## <a name="use-the-custom-renderer-pattern"></a>使用自定义呈现器模式

大多数 Xamarin.Forms 呈现器类会公开 `OnElementChanged` 方法，此方法会在创建 Xamarin.Forms 自定义控件时被调用以呈现相应的本机控件。 自定义呈现器类（在每个平台项目中）随后会替代此方法，以实例化并自定义本机控件。 `SetNativeControl` 方法用于实例化本机控件，此方法还会将控件引用分配给 `Control` 属性。

但是，在某些情况下，可以多次调用 `OnElementChanged` 方法。 因此，为了防止内存泄漏（这可能会对性能产生影响），在实例化新的本机控件时务必要格外小心。 下面的代码示例中演示了在自定义呈现器中实例化新的本机控件时要使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {
    if (Control == null)
    {
      // Instantiate the native control with the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

当 `Control` 属性是 `null` 时，新的本机控件只应实例化一次。 此外，仅当自定义呈现器附加到新 Xamarin.Forms 元素时，才应创建、配置该控件并订阅事件处理程序。 同样，仅当呈现器所附加到的元素更改时，才应取消订阅任何订阅的事件处理程序。 采用此方法将有助于创建不会遭受内存泄漏的高效执行的自定义呈现器。

> [!IMPORTANT]
> 仅当 `e.NewElement` 属性不是 `null` 且 `Control` 属性为`null` 时，才应调用 `SetNativeControl` 方法。

有关自定义呈现器的详细信息，请参阅[在每个平台上自定义控件](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="related-links"></a>相关链接

- [跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [正在编译 XAML](~/xamarin-forms/xaml/xamlc.md)
- [编译的绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [快速呈现器](~/xamarin-forms/internals/fast-renderers.md)
- [布局压缩](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)
- [ListView 性能](~/xamarin-forms/user-interface/listview/performance.md)
- [优化图像资源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [XAML 样式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [自定义每个平台上的控件](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
