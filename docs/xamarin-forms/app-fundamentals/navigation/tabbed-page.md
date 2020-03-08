---
title: Xamarin.Forms TabbedPage
description: Xamarin.Forms TabbedPage 包含一系列选项卡和较大的详细信息区域，其中每个选项卡都将内容加载到详细信息区域。 本文演示如何使用 TabbedPage 浏览页集合。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910708"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.Forms TabbedPage

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 包含一系列选项卡和较大的详细信息区域，其中每个选项卡都将内容加载到详细信息区域。 以下屏幕截图显示 iOS 和 Android 上的 `TabbedPage`：

[![iOS 和 Android 上包含三个选项卡的 TabbedPage 的屏幕截图](tabbed-page-images/tabbedpage-today.png "带有三个选项卡的 TabbedPage")](tabbed-page-images/tabbedpage-today-large.png#lightbox "带有三个选项卡的 TabbedPage")

在 iOS 上，选项卡的列表显示在屏幕的底部，而详细信息区域显示在上方。 每个选项卡都包含一个标题和一个图标，该图标应为带有 alpha 通道的 PNG 文件。 在纵向方向，选项卡栏图标显示在选项卡标题上方。 在横向方向，图标和标题并排显示。 此外，可以根据设备和方向显示常规或精简选项卡栏。 如果有五个以上的选项卡，会显示“更多”选项卡，可用于访问其他选项卡。 有关图标要求的信息，请参阅 developer.apple.com 上的[选项卡栏图标大小](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size)。

> [!TIP]
> `TabbedRenderer` for iOS 有可替代的 `GetIcon` 方法，可以用来从指定的源加载选项卡图标。 使用此替代方法可以将 SVG 图像用作 `TabbedPage` 上的图标。 此外，可以提供选定和未选定版本的图标。

在 Android 上，选项卡列表显示在屏幕顶部，详细信息区域显示在下方。 每个选项卡都包含一个标题和一个图标，该图标应为带有 alpha 通道的 PNG 文件。 但是，可以使用特定于平台布局将选项卡移动至屏幕底部。 如果有五个以上的选项卡，并且选项卡列表位于屏幕底部，会显示“更多”选项卡，可用于访问其他选项卡。 有关图标要求的信息，请参阅 material.io 上的[选项卡](https://material.io/components/tabs/#)和 developer.android.com 上的[支持不同的像素密度](https://developer.android.com/training/multiscreen/screendensities)。 有关将选项卡移动到屏幕底部的信息，请参阅[设置 TabbedPage 工具栏位置和颜色](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md)。

> [!TIP]
> `TabbedPageRenderer` for Android AppCompat 有可替代的 `GetIconDrawable` 方法，可以用来从自定义 `Drawable` 加载选项卡图标。 使用此替代方法可以将 SVG 图像用作 `TabbedPage` 上的图标，并且可以使用顶部和底部选项卡。 或者，可替代的 `SetTabIcon` 方法可用于从顶部标签栏的自定义 `Drawable` 加载选项卡图标。

在通用 Windows 平台 (UWP) 上，选项卡列表显示在屏幕顶部，详细信息区域显示在下方。 每个选项卡都包含一个标题。 但是，可以使用特定于平台布局向每个选项卡添加图标。 有关详细信息，请参阅 [Windows 上的 TabbedPage 图标](~/xamarin-forms/platform/windows/tabbedpage-icons.md)。

## <a name="create-a-tabbedpage"></a>创建 TabbedPage

可以使用两种方法创建 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

- 使用子 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 对象的集合（例如 [`Page`](xref:Xamarin.Forms.Page) 的集合）来填充 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 有关详细信息，请参阅[使用页面集合填充 TabbedPage](#populate-a-tabbedpage-with-a-page-collection)。
- 将集合分配给 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 属性，并将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 分配给 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性以返回集合中对象的页面。 有关详细信息，请参阅[使用模板填充 TabbedPage](#populate-a-tabbedpage-with-a-template)。

使用这两种方法，[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 将在用户选择每个标签时显示每个页面。

> [!IMPORTANT]
> 建议仅使用 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 和 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例填充 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 这将有助于确保在所有平台上都有一致的用户体验。

此外，[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 定义以下属性：

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor) 类型的 [`Color`](xref:Xamarin.Forms.Color)，选项卡栏的背景色。
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor) 类型的 [`Color`](xref:Xamarin.Forms.Color)，选项卡栏上文本的颜色。
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) 类型的 [`Color`](xref:Xamarin.Forms.Color)，选定选项卡后选项卡的颜色。
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) 类型的 [`Color`](xref:Xamarin.Forms.Color)，未选定选项卡时选项卡的颜色。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着可设置他们的样式，并且这些属性可以作为数据绑定的目标。

> [!WARNING]
> 在 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 中，构造 [`Page`](xref:Xamarin.Forms.Page) 时，将创建每个 `TabbedPage` 对象。 这可能会导致用户体验不佳，尤其是在 `TabbedPage` 是应用程序的根页面时。 但是，Xamarin.Forms Shell 可以根据需要创建通过选项卡栏访问的页面，以响应导航。 有关详细信息，请参阅 [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>使用页集合填充 TabbedPage

可以使用子 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 对象的集合（例如 [`Page`](xref:Xamarin.Forms.Page) 的集合）来填充 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 可通过将 `Page` 对象添加到 [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) 集合来实现此操作。 这可以在 XAML 中实现，如下所示：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

> [!NOTE]
> 派生出 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) 的 [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) 类的 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 属性是 `ContentProperty` 的 `MultiPage<T>`。 因此，在 XAML 中，无需将 [`Page`](xref:Xamarin.Forms.Page) 对象显式分配给 `Children` 属性。

等效 C# 代码如下：

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    NavigationPage navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

在此示例中，使用两个 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 对象填充 [`Page`](xref:Xamarin.Forms.ContentPage)。 第一个子级是 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象，第二个子级是包含 [ 对象的 `NavigationPage`](xref:Xamarin.Forms.NavigationPage)`ContentPage`。

以下屏幕截图显示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 中的 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 对象：

[![iOS 和 Android 上包含三个选项卡的 TabbedPage 的屏幕截图](tabbed-page-images/tabbedpage-today.png "带有三个选项卡的 TabbedPage")](tabbed-page-images/tabbedpage-today-large.png#lightbox "带有三个选项卡的 TabbedPage")

选择另一个选项卡将显示表示该选项卡的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象：

[![iOS 和 Android 上包含选项卡的 TabbedPage 的屏幕截图](tabbed-page-images/tabbedpage-week.png "带有选项卡的 TabbedPage")](tabbed-page-images/tabbedpage-week-large.png#lightbox "带有选项卡的 TabbedPage")

在“计划”选项卡上，[`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象包装在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 对象中。

> [!WARNING]
> 虽然 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 可以放置在 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 中，但不建议将 `TabbedPage` 放置到 `NavigationPage` 中。 这是因为，在 iOS 上，`UITabBarController` 始终充当 `UINavigationController` 的包装器。 有关详细信息，请参阅 iOS 开发人员库中的[组合的视图控制器接口](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html)。

## <a name="navigate-within-a-tab"></a>在选项卡中导航

导航可以在选项卡中执行，前提是 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象包装在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 对象中。 可以通过在 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 对象的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性上调用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 方法来实现此操作：

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

要导航到的页面被指定为 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 方法的参数。 在此示例中，将 `UpcomingAppointmentsPage` 页推送到导航堆栈中，在堆栈中，它成为活动页：

[![iOS 和 Android 上的选项卡中的导航屏幕截图](tabbed-page-images/tabbedpage-upcoming.png "选项卡中的 TabbedPage 导航")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "选项卡中的 TabbedPage 导航")

有关使用 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类执行导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="populate-a-tabbedpage-with-a-template"></a>使用模板填充 TabbedPage

通过将数据集合分配给 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 属性，并将 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 分配给将数据模板化为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 对象的 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性，可以用页填充 [`Page`](xref:Xamarin.Forms.Page)。 这可以在 XAML 中实现，如下所示：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage"
            ItemsSource="{x:Static local:MonkeyDataModel.All}">            
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

等效 C# 代码如下：

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() =>
    {
      var nameLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage
      {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children =
          {
            nameLabel,
            image,
            new StackLayout
            {
              Padding = new Thickness (50, 10),
              Children =
              {
                new StackLayout
                {
                  Orientation = StackOrientation.Horizontal,
                  Children =
                  {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                // ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

在此示例中，每个选项卡都包含一个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象，该对象使用 [`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 对象来显示该选项卡的数据：

[![iOS 和 Android 上的模板化 TabbedPage 的屏幕截图](tabbed-page-images/tabbedpage-template.png "模板化 TabbedPage")](tabbed-page-images/tabbedpage-template-large.png#lightbox "模板化 TabbedPage")

选择另一个选项卡将显示表示该选项卡的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象。

## <a name="related-links"></a>相关链接

- [TabbedPageWithNavigationPage（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPage API](xref:Xamarin.Forms.TabbedPage)
