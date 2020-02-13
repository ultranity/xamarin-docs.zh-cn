---
title: Xamarin.Forms 快速入门深入探讨
description: 本文介绍了使用 Xamarin.Forms 开发应用程序的基础知识。 涵盖的主题包括：Xamarin.Forms 应用程序剖析、体系结构和应用程序基础知识以及用户界面。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: c0e8ec27898cc842d485967e525c2936d7a0f56d
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131054"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms 快速入门深入探讨

在 [Xamarin.Forms 快速入门](~/get-started/index.yml)中，生成了 Notes 应用程序。 本文对已生成的内容进行回顾，以深入了解有关 Xamarin.Forms 应用程序工作原理的基础知识。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 简介

Visual Studio 将代码组织为解决方案  和项目  。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序、支持库、测试应用程序等。 Notes 应用程序包含 1 个内附 4 个项目的解决方案，如以下屏幕截图所示：

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

这些项目如下：

- Notes - 此项目是 .NET Standard 库项目，其中包含所有共享代码和共享 UI。
- Notes.Android - 此项目包含 Android 特定代码，是 Android 应用程序的入口点。
- Notes.iOS - 此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。
- Notes.UWP - 此项目包含通用 Windows 平台特定代码，是 UWP 应用程序的入口点。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 应用程序剖析

以下屏幕截图显示 Visual Studio 中 Notes .NET Standard 库项目的内容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

项目具有包含 NuGet 和 SDK 节点的依赖项节点    ：

- **NuGet** &ndash; 已添加到项目的 Xamarin.Forms 和 sqlite-net-pcl NuGet 包。
- **SDK** &ndash; `NETStandard.Library` 元包，它引用定义 .NET Standard 的一整套 NuGet 包。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 简介

[Visual Studio for Mac](/visualstudio/mac/) 遵循将代码组织为解决方案和项目的 Visual Studio 做法   。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序、支持库、测试应用程序等。 Notes 应用程序包含 1 个内附 3 个项目的解决方案，如以下屏幕截图所示：

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

这些项目如下：

- Notes - 此项目是 .NET Standard 库项目，其中包含所有共享代码和共享 UI。
- Notes.Droid - 此项目包含 Android 特定代码，是 Android 应用程序的入口点。
- Notes.iOS - 此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 应用程序剖析

以下屏幕截图显示 Visual Studio for Mac 中 Notes .NET Standard 库项目的内容：

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

项目具有包含 NuGet 和 SDK 节点的依赖项节点    ：

- **NuGet** &ndash; 已添加到项目的 Xamarin.Forms 和 sqlite-net-pcl NuGet 包。
- **SDK** &ndash; `NETStandard.Library` 元包，它引用定义 .NET Standard 的一整套 NuGet 包。

::: zone-end

项目还包括多个文件：

- **Data\NoteDatabase.cs** – 此类包含用于创建数据库、从中读取数据、向其写入数据以及从中删除数据的代码。
- **Models\Note.cs** – 此类定义一个 `Note` 模型，其实例在应用程序中存储有关每个便笺的数据。
- **App.xaml** - `App` 类的 XAML 标记，该类定义应用程序的资源字典。
- **App.xaml.cs** - `App` 类的代码隐藏，该类负责实例化应用程序在每个平台上将显示的首页，并处理应用程序生命周期事件。
- **AssemblyInfo.cs** –此文件包含在程序集级别应用的有关项目的应用程序属性。
- **NotesPage.xaml** - `NotesPage` 类的 XAML 标记，该类定义应用程序启动时所显示页的 UI。
- **NotesPage.xaml.cs** - `NotesPage` 类的代码隐藏，该类包含用户与页面交互时执行的业务逻辑。
- **NoteEntryPage.xaml** - `NoteEntryPage` 类的 XAML 标记，该类定义用户输入便笺时所显示页的 UI。
- **NoteEntryPage.xaml.cs** - `NoteEntryPage` 类的代码隐藏，该类包含用户与页面交互时执行的业务逻辑。

有关 Xamarin.iOS 应用程序剖析的详细信息，请参阅 [Xamarin.iOS 应用程序剖析](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application)。 有关 Xamarin.Android 应用程序剖析的详细信息，请参阅 [Xamarin Android 应用程序剖析](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>体系结构和应用程序基础知识

Xamarin.Forms 应用程序采用与传统跨平台应用程序相同的构建方式。 共享代码通常位于 .NET Standard 库中，平台特定应用程序将使用此共享代码。 下图概要演示了 Notes 应用程序的这种关系：

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

若要最大限度重用启动代码，Xamarin.Forms 应用程序需有一个名为 `App` 的单个类，该类负责实例化应用程序在每个平台上将显示的首页，如以下代码示例所示：

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

此代码将 `App` 类的 `MainPage` 属性设置为 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例，其内容是 `NotesPage` 实例。

此外，AssemblyInfo.cs  文件包含在程序集级别应用的单个应用程序属性：

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

[`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 属性可打开 XAML 编译器，以使 XAML 直接编译为中间语言。 有关详细信息，请参阅 [XAML 编译](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>在每个平台上启动应用程序

### <a name="ios"></a>iOS

为了在 iOS 中启动初始 Xamarin.Forms 页面，Notes.iOS 项目定义了继承自 `FormsApplicationDelegate` 类的 `AppDelegate` 类：

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

通过调用 `Init` 方法，`FinishedLaunching` 替代会初始化 Xamarin.Forms 框架。 这会导致在通过调用 `LoadApplication` 方法来设置根视图控制器之前，先将特定于 iOS 的 Xamarin.Forms 实现加载到应用程序。

### <a name="android"></a>Android

为了在 Android 中启动 Xamarin.Forms 初始页面，Notes.Droid 项目包括使用 `MainLauncher` 属性创建 `Activity` 的代码，以及继承自 `FormsAppCompatActivity` 类的活动：

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

通过调用 `Init` 方法，`OnCreate` 替代会初始化 Xamarin.Forms 框架。 这会导致在加载 Xamarin.Forms 应用程序之前，将特定于 Android 的 Xamarin.Forms 实现加载到应用程序。

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 应用程序中，可从 `App` 类调用初始化 Xamarin.Forms 框架的 `Init` 方法：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

这会将特定于 UWP 的 Xamarin.Forms 实现加载到应用程序。 初始 Xamarin.Forms 页面通过 `MainPage` 类启动：

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

可通过 `LoadApplication` 方法加载 Xamarin.Forms 应用程序。

> [!NOTE]
> 通用 Windows 平台应用可以使用 Xamarin.Forms 生成，但只能在 Windows 上使用 Visual Studio。

::: zone-end

## <a name="user-interface"></a>用户界面

可使用 4 个主要控件组创建 Xamarin.Forms 应用程序的用户界面：

1. **页面** - Xamarin.Forms 页呈现跨平台移动应用程序屏幕。 Notes 应用程序使用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 类显示单个屏幕。 有关页面的详细信息，请参阅 [Xamarin.Forms 页面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **视图** - Xamarin.Forms 视图是显示在用户界面上的控件，如标签、按钮和文本输入框。 已完成的 Notes 应用程序使用 [`ListView`](xref:Xamarin.Forms.ListView)、[`Editor`](xref:Xamarin.Forms.Editor) 和 [`Button`](xref:Xamarin.Forms.Button) 视图。 有关视图的详细信息，请参阅 [Xamarin.Forms 视图](~/xamarin-forms/user-interface/controls/views.md)。
1. **布局** - Xamarin.Forms 布局是用于将视图组合到逻辑结构的容器。 Notes 应用程序使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 类采用垂直叠放来排列视图，并使用 [`Grid`](xref:Xamarin.Forms.Grid) 类水平排列按钮。 有关布局的详细信息，请参阅 [Xamarin.Forms 布局](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **单元格** - Xamarin.Forms 单元格是专门用于列表中的项的元素，描述列表中每个项的绘制方式。 Notes 应用程序使用 [`TextCell`](xref:Xamarin.Forms.TextCell) 为列表中的每行显示两个项。 有关单元格的详细信息，请参阅 [Xamarin.Forms 单元格](~/xamarin-forms/user-interface/controls/cells.md)。

在运行时，每个控件都会映射到其本身的本机等效项（即呈现的内容）。

### <a name="layout"></a>布局

Notes 应用程序使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 在屏幕上自动排列视图而不考虑屏幕大小，从而简化了跨平台应用程序开发。 根据添加顺序，以垂直方式或水平方式逐个放置每个子元素。 `StackLayout` 使用的空间大小取决于 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性的设置方式，但默认情况下，`StackLayout` 尝试使用整个屏幕。

以下 XAML 代码举例说明了如何使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 对 `NoteEntryPage` 进行布局：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

默认情况下，[`StackLayout`](xref:Xamarin.Forms.StackLayout) 采用垂直方向。 但是，可以通过将 [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 属性设置为 [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) 枚举成员来更改为水平方向。

> [!NOTE]
> 可以通过 `HeightRequest` 和 `WidthRequest` 属性设置视图的大小。

有关 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 类的详细信息，请参阅 [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

### <a name="responding-to-user-interaction"></a>响应用户交互

XAML 中定义的对象可触发在隐藏文件中处理的事件。 以下代码示例演示了 `NoteEntryPage` 类的代码隐藏中的 `OnSaveButtonClicked` 方法，执行该方法是为了响应在“保存”  按钮上触发的 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件。

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

`OnSaveButtonClicked` 方法将便笺保存在数据库中，并导航回上一页。

> [!NOTE]
> XAML 类的代码隐藏文件可使用为其分配的、具有 `x:Name` 属性的名称访问 XAML 中定义的对象。 分配给此属性的值与 C# 变量的规则相同，因为该值必须以字母或下划线开头，且不包含嵌入的空格。

在 `NoteEntryPage` 类的 XAML 标记中将保存按钮与 `OnSaveButtonClicked` 方法关联起来：

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>列表

[`ListView`](xref:Xamarin.Forms.ListView) 负责在列表中重置显示项的集合。 `ListView` 中的每个项会包含在单个单元格中。

下面的代码示例演示来自 `NotesPage` 的 [`ListView`](xref:Xamarin.Forms.ListView)：

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

[`ListView`](xref:Xamarin.Forms.ListView) 中每行的布局在 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 元素中定义，并使用数据绑定显示应用程序检索到的任何便笺。 [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 属性在 `NotesPage.xaml.cs` 中设置为数据源：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

此代码使用存储在数据库中的任何便笺填充 [`ListView`](xref:Xamarin.Forms.ListView)。

在 [`ListView`](xref:Xamarin.Forms.ListView) 中选择了一行时，会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 当事件触发时，会执行名为 `OnListViewItemSelected` 的事件处理程序：

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件可以通过 [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 属性访问与单元格关联的对象。

有关 [`ListView`](xref:Xamarin.Forms.ListView) 类的详细信息，请参阅 [ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="navigation"></a>导航

Xamarin.Forms 提供多种不同的页导航体验，具体取决于使用的 [`Page`](xref:Xamarin.Forms.Page) 类型。 对于 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例，导航可以是分层或模式的。 有关模式导航的信息，请参阅 [Xamarin.Forms 模式页面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 类提供替代导航体验。 有关详细信息，请参阅[导航](~/xamarin-forms/app-fundamentals/navigation/index.md)。

在分层导航中，[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类用于在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象的堆栈内根据需要向前和向后进行导航。 此类将导航实现为 [`Page`](xref:Xamarin.Forms.Page) 对象的后进先出 (LIFO) 堆栈。 若要从一页移动到另一页，应用程序会将新页推送到导航堆栈中，在堆栈中，该页会变为活动页。 若要返回到前一页，应用程序会从导航堆栈弹出当前页，而使最顶层的页成为活动页。

`NavigationPage` 类还会将导航栏添加到页面顶部，此页面显示标题和平台相应的“返回”  按钮，通过此按钮可返回上一页。

添加到导航堆栈中的第一页称为应用程序的根  页，以下代码示例显示了在 Notes 应用程序中实现此过程的方法：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例都具有 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性，可提供修改页面堆栈的方法。 应仅当应用程序包括 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 时，才调用这些方法。 若要导航到 `NoteEntryPage`，则必须调用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) 方法，如下面的代码示例中所示：

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

这会将新的 `NoteEntryPage` 对象推送到导航堆栈中，在堆栈中，它成为活动页。

通过设备上的返回  按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。 若要以编程方式返回原始页，`NoteEntryPage` 对象必须调用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如下面的代码示例中所示：

```csharp
await Navigation.PopAsync();
```

有关分层导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="data-binding"></a>数据绑定

数据绑定用于简化 Xamarin.Forms 应用程序显示及其与数据的交互方式。 它将在用户界面和基础应用程序之间建立连接。 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 类包含大部分基础结构以支持数据绑定。

数据绑定连接两个对象，即源  和目标  。 源  对象提供数据。 目标  对象使用（并经常显示）来自源对象的数据。 例如，[`Editor`](xref:Xamarin.Forms.Editor)（目标  对象）通常会将其 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性绑定到源  对象中的公共 `string` 属性。 下图说明了这种绑定关系：

![](deepdive-images/data-binding.png "Data Binding")

数据绑定的主要优点是让你无需再担心视图和数据源之间的数据同步。 幕后的绑定框架源会将源  对象中的更改自动推送到目标  对象，且目标对象中的更改可选择性地推送回源  对象。

创建数据绑定只需两个步骤：

- 目标  对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性必须设置为源  。
- 必须在目标  和源  之间建立绑定。 在 XAML 中，此过程可通过使用 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 标记扩展实现。

在 Notes 应用程序中，绑定目标是显示便笺的 [`Editor`](xref:Xamarin.Forms.Editor)，而设置为 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的 `Note` 实例是绑定源。

`NoteEntryPage` 的 `BindingContext` 在页面导航过程中进行设置，如下面的代码示例所示：

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

在 `OnNoteAddedClicked` 方法（向应用程序添加新便笺时执行）中，`NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为新 `Note` 实例。 在 `OnListViewItemSelected` 方法（在 [`ListView`](xref:Xamarin.Forms.ListView)中选择现有便笺时执行）中，`NoteEntryPage` 的 `BindingContext` 设置为所选 `Note` 实例，该实例通过 [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 属性进行访问。

> [!IMPORTANT]
> 虽然可以分别设置每个目标  对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性，但没有必要。 `BindingContext` 是特殊属性，其所有子级都会继承该属性。 因此，当 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上的 `BindingContext` 设置为 `Note` 实例时，`ContentPage` 的所有子级都具有相同的 `BindingContext`，并且都可绑定到 `Note` 对象的公共属性。

`NoteEntryPage` 中的 [`Editor`](xref:Xamarin.Forms.Editor) 随后会绑定到 `Note` 对象的 `Text` 属性：

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

在 [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) 属性和源  对象的 `Text` 属性之间建立绑定。 `Editor` 中所做的更改将自动传播到 `Note` 对象。 同样，如果更改了 `Note.Text` 属性，Xamarin.Forms 绑定引擎也会更新 `Editor` 的内容。 这称为双向绑定  。

有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="styling"></a>“样式”

Xamarin.Forms 应用程序通常包含多个具有相同外观的视觉对象元素。 设置每个视觉对象元素的外观可能是重复性的并且容易出错。 相反，可以创建用于定义外观的样式，然后将其应用于所需的视觉对象元素。

[`Style`](xref:Xamarin.Forms.Style) 类将属性值的集合分组到一个对象中，然后可以将该对象应用于多个视觉对象元素实例。 样式以应用程序级别、页面级别或视图级别存储在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中。 选择在何处定义 `Style` 会影响其应用范围：

- 在应用程序级别定义的 [`Style`](xref:Xamarin.Forms.Style) 实例可以应用于整个应用程序。
- 在页面级别定义的 [`Style`](xref:Xamarin.Forms.Style) 实例可以应用于页面及其子级。
- 在视图级别定义的 [`Style`](xref:Xamarin.Forms.Style) 实例可以应用于视图及其子级。

> [!IMPORTANT]
> 在整个应用程序中使用的任何样式都存储在应用程序的资源字典中以避免重复。 但是，特定于页面的 XAML 不应包含在应用程序的资源字典中，因为这些资源随后会在应用程序启动时（而不是页面需要时）进行分析。

每个 [`Style`](xref:Xamarin.Forms.Style) 实例都包含一个或多个 [`Setter`](xref:Xamarin.Forms.Setter) 对象的集合，其中每个 `Setter` 都具有 [`Property`](xref:Xamarin.Forms.Setter.Property) 和 [`Value`](xref:Xamarin.Forms.Setter.Value)。 `Property` 是应用样式的元素的可绑定属性的名称，而 `Value` 是应用于属性的值。 下面的代码示例演示来自 `NoteEntryPage` 的样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

此样式应用于页面上的任何 [`Editor`](xref:Xamarin.Forms.Editor) 实例。

创建 [`Style`](xref:Xamarin.Forms.Style) 时，始终需要 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 属性。

> [!NOTE]
> 在传统上，使用 XAML 样式对 Xamarin.Forms 应用程序设置样式。 但是，Xamarin.Forms 还支持使用级联样式表 (CSS) 对视觉对象元素设置样式。 有关详细信息，请参阅[使用级联样式表 (CSS) 设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/css/index.md)。

有关 XAML 样式的详细信息，请参阅[使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### <a name="providing-platform-specific-styles"></a>提供特定于平台的样式

通过 `OnPlatform` 标记扩展可基于每个平台自定义 UI 外观：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

此 [`Style`](xref:Xamarin.Forms.Style) 根据所使用的平台，为 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的 [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) 和 [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) 属性设置不同的 [`Color`](xref:Xamarin.Forms.Color) 值。

若要深入了解 XAML 标记扩展，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。 有关 `OnPlatform` 标记扩展的信息，请参阅 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="testing-and-deployment"></a>测试和部署

Visual Studio for Mac 和 Visual Studio 均提供许多用于测试和部署应用程序的选项。 调试应用程序是应用程序开发生命周期的普遍一环，有助于诊断代码问题。 有关详细信息，请参阅[设置断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步执行代码](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)和[日志窗口的输出信息](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

模拟器是开始部署和测试应用程序的有利位置，其提供用于测试应用程序的有用功能。 但是，用户不会在模拟器中使用最终应用程序，因此应尽早并经常在实际设备上测试应用程序。 有关 iOS 设备预配的详细信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)。 有关 Android 设备预配的详细信息，请参阅[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="next-steps"></a>后续步骤

本深入探讨介绍了使用 Xamarin.Forms 开发应用程序的基础知识。 建议的后续步骤包括了解以下功能：

- 可使用 4 个主要控件组创建 Xamarin.Forms 应用程序的用户界面。 有关详细信息，请参阅[控件引用](~/xamarin-forms/user-interface/controls/index.md)。
- 数据绑定将两个对象的属性链接起来，对某一属性的更改就会自动反映在另一个属性中。 有关详细信息，请参阅[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms 提供多种不同的页面导航体验，具体取决于所使用的页面类型。 有关详细信息，请参阅[导航](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 样式有助于减少重复的标记，并且使用样式可以更轻松地更改应用程序的外观。 有关详细信息，请参阅[设计 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/index.md)。
- XAML 标记扩展通过支持从文本字符串以外的源设置元素属性，扩展 XAML 的功能和灵活性。 有关详细信息，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。
- 数据模板让你可以在支持的视图上定义数据表示形式。 有关详细信息，请参阅[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 通过 `Renderer` 类可以在每个平台上以不同方式呈现每个页面、布局和视图，反过来又可以创建本机控件，在屏幕上排列该控件，并添加共享代码中指定的行为。 开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 有关详细信息，请参阅[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 还可以自定义每个平台上的本机控件的效果。 通过子类化 [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) 类在特定于平台的项目中创建效果，并将其附加到相应的 Xamarin.Forms 控件中使用。 有关详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 共享代码可通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 类访问本机功能。 有关详细信息，请参阅[通过 DependencyService 访问本机功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

此外，也可以阅读 Charles Petzold 撰写的[使用 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)一书，了解有关 Xamarin.Forms 的详细信息。  可获取此书的 PDF 版本或多种电子书格式的版本。

## <a name="related-links"></a>相关链接

- [可扩展应用程序标记语言 (XAML)](~/xamarin-forms/xaml/index.yml)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [控件引用](~/xamarin-forms/user-interface/controls/index.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [入门示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Xamarin.Forms API 参考](xref:Xamarin.Forms)
- [免费自学教程（视频）](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
