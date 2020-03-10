---
title: 分层导航
description: 本文演示如何使用 NavigationPage 类在后进先出 (LIFO) 页面的堆栈中执行导航。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 11ad1fb18d1263eb77ef037350a3633510934c42
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915682"
---
# <a name="hierarchical-navigation"></a>分层导航

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_NavigationPage 类提供了分层的导航体验，用户可以根据需要在页面间进行导航。类实现导航作为 Page 对象的后进先出（LIFO）堆栈。本文演示如何使用 NavigationPage 类在一叠页面中执行导航。_

若要从一页移动到另一页，应用程序会将新页推送到导航堆栈中，在堆栈中，该页会变为活动页，如以下关系图中所示：

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

若要返回到前一页，应用程序会从导航堆栈弹出当前页面，而使最顶层的新页面成为活动页面，如以下关系图中所示：

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

可以由任何 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 派生类型上的 [`Page`](xref:Xamarin.Forms.Page) 属性公开导航方法。 这些方法能够将页面推送到导航堆栈、从导航堆栈中弹出页面以及执行堆栈操作。

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>执行导航

在分层导航中，使用 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象的堆栈内进行导航。 以下屏幕截图显示了每个平台上 `NavigationPage` 的主要组件：

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的布局取决于平台：

- 在 iOS 中，页面顶部有一个显示标题的导航栏，其上有一个“Back”按钮，可以返回前一页。
- 在 Android 中，页面顶部有一个显示标题、图标的导航栏，其上有一个“Back”按钮，可以返回前一页。 在 `[Activity]` 属性中定义图标，该属性修饰特定于 Android 平台的项目中的 `MainActivity` 类。
- 在通用 Windows 平台上，显示标题的页面顶部有一个导航栏。

在所有平台上，[`Page.Title`](xref:Xamarin.Forms.Page.Title) 属性的值将显示为页面标题。

> [!NOTE]
> 建议只使用 `NavigationPage` 实例填充 `ContentPage`。

### <a name="creating-the-root-page"></a>创建根页

添加到导航堆栈中的第一页称为应用程序的根页，以下代码示例显示了实现此过程的方法：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

这会使 `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage)实例被推送到导航堆栈上，该堆栈会成为应用程序的活动页和根页。 以下屏幕截图演示了此过程：

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) 实例的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 属性可提供对导航堆栈中的第一页的访问权限。

### <a name="pushing-pages-to-the-navigation-stack"></a>将页面推送到导航堆栈

若要导航到 `Page2Xaml`，需要对当前页的 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 属性调用 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 方法，如以下代码示例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

这会将 `Page2Xaml` 实例推送到导航堆栈中，在堆栈中，它成为活动页。 以下屏幕截图演示了此过程：

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

调用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 方法后，会发生以下事件：

- 调用 `PushAsync` 的页面会调用其 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 替代。
- 要导航到的页面会调用其 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 替代。
- `PushAsync` 任务完成。

但是，这些事件发生的确切顺序取决于平台。 有关详细信息，请参阅 Charles Petzold 所著 Xamarin.Forms 书籍的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

> [!NOTE]
> 不能将对 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 和 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 替代的调用视为绝对的页面导航指示。 例如，在 iOS 上，应用程序终止后，将对活动页面调用 `OnDisappearing` 替代。

### <a name="popping-pages-from-the-navigation-stack"></a>从导航堆栈中弹出页面

通过设备上的返回按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。

若要以编程方式返回原始页，`Page2Xaml` 实例必须调用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如以下代码示例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

这会从导航堆栈中删除 `Page2Xaml` 实例，而使最顶层的页成为活动页。 调用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法后，会发生以下事件：

- 调用 `PopAsync` 的页面会调用其 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 替代。
- 要返回到的页面会调用其 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 替代。
- `PopAsync` 任务返回。

但是，这些事件发生的确切顺序取决于平台。 有关详细信息，请参阅 Charles Petzold 所著 Xamarin.Forms 书籍的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

除了 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 和 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法之外，每个页面的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性还提供了 [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) 方法，如下面的代码示例所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

该方法将弹出导航堆栈中除根 [`Page`](xref:Xamarin.Forms.Page) 之外的所有内容，从而使应用程序的根页面成为活动页面。

### <a name="animating-page-transitions"></a>对页面过渡效果进行动画处理

每个页面的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性还提供已替代的 push 和 pop 方法，这些方法包含一个 `boolean` 参数，用于控制是否在导航期间显示页面动画，如以下代码示例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

将 `boolean` 参数设置为 `false` 会禁用页面过渡动画，而将参数设置为 `true` 则会启用页面过渡动画，前提是基础平台支持该动画。 但是，缺少此参数的 push 和 pop 方法默认启用该动画。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>导航时传递数据

有时，页面必须在导航期间将数据传递到另一个页面。 实现此操作的两种方法是：通过页面构造函数传递数据，将新页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为该数据。 下面将依次讨论每种方法。

### <a name="passing-data-through-a-page-constructor"></a>通过页面构造函数传递数据

在导航期间，通过页面构造函数参数将数据传递到另一个页面是最简单的方法，如以下代码示例所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此代码创建一个 `MainPage` 实例，以 ISO8601 格式传递当前日期和时间，该格式包装在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例中。

`MainPage` 实例通过构造函数参数接收数据，如以下代码示例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

然后，通过设置 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 属性，在页面上显示数据，如以下屏幕截图所示：

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>通过 BindingContext 传递数据

在导航期间，另一种将数据传递到另一个页面的方法是将新页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为该数据，如以下代码示例所示：

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

此代码将 [ 实例的 `BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)`SecondPage` 设置为 `Contact` 实例，然后导航到 `SecondPage`。

然后，`SecondPage` 使用数据绑定来显示 `Contact` 实例数据，如以下 XAML 代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

以下代码示例显示了如何在 C# 中完成数据绑定：

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

然后，数据会通过一系列 [`Label`](xref:Xamarin.Forms.Label) 控件显示在页面上，如以下屏幕截图所示：

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>操作导航堆栈

可使用 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性公开 [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) 属性，并从中获得导航堆栈中的页面。 当 Xamarin.Forms 维护对导航堆栈的访问时，`Navigation` 属性提供 [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) 和 [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) 方法，用于通过插入页面或将其删除来操作堆栈。

[`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) 方法将导航堆栈中的指定页插入到现有指定页之前，如下图所示：

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

[`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) 方法可从导航堆栈中删除指定页面，如下图所示：

![](hierarchical-images/remove-page.png "Removing a Page from the Navigation Stack")

这些方法支持自定义导航体验，例如在成功登录后将登录页面替换为新页面。 以下代码示例演示了此方案：

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

假定用户的凭据正确，则会将 `MainPage` 实例插入到当前页面之前的导航堆栈中。 然后，[`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法将从导航堆栈中删除当前页面，`MainPage` 实例将成为活动页面。

## <a name="displaying-views-in-the-navigation-bar"></a>在导航栏中显示视图

所有 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 都可以显示在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的导航栏中。 这是通过将 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加属性设置为 `View` 来实现的。 此附加属性可以在任何 [`Page`](xref:Xamarin.Forms.Page) 上设置，当 `Page` 被推送到 `NavigationPage` 上后，`NavigationPage` 会遵守属性的值。

下面的示例取自[标题视图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)，演示如何从 XAML 中设置 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

下面是等效 C# 代码：

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

这导致 [`Slider`](xref:Xamarin.Forms.Slider) 显示在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的导航栏中：

[![滑块 TitleView](hierarchical-images/titleview-small.png "滑块 TitleView")](hierarchical-images/titleview-large.png#lightbox "滑块 TitleView")

> [!IMPORTANT]
> 很多视图不会出现在导航栏中，除非使用 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性指定视图的大小。 或者，可以将视图包装在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中，并将 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为适当的值。

注意，因为 [`Layout`](xref:Xamarin.Forms.Layout) 类派生自 [`View`](xref:Xamarin.Forms.View) 类，所以可以设置 [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加属性来显示包含多个视图的布局类。 在 iOS 和通用 Windows 平台 (UWP) 上，导航条的高度是不能更改的，所以如果导航条中显示的视图大于导航条的默认大小，就会发生剪裁。 但是，在 Android 上，可以通过将 [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) 可绑定属性设置为表示新高度的 `double` 来更改导航条的高度。 有关详细信息，请参阅[在 NavigationPage 上设置导航栏高度](~/xamarin-forms/platform/android/navigationpage-bar-height.md)。

或者，可以通过在导航栏中放置一些内容，以及在与导航栏颜色匹配的页面内容顶部的视图中放置一些内容，来建议扩展导航条。 此外，在 iOS 中，导航栏底部的分隔线和阴影可以通过将 [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) 可绑定属性设置为 `true` 来移除。 有关详细信息，请参阅[在 NavigationPage 上隐藏导航栏分隔符](~/xamarin-forms/platform/ios/navigation-bar-separator.md)。

> [!NOTE]
> [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty)、[`Title`](xref:Xamarin.Forms.Page.Title)、[`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) 和 [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 属性都可以定义占用导航栏空间的值。 虽然导航栏大小因平台和屏幕大小而异，但由于可用空间有限，设置所有这些属性将导致冲突。 你可能会发现，与其尝试使用这些属性的组合，不如仅通过设置 `TitleView` 属性来更好地实现所需的导航栏设计。

### <a name="limitations"></a>限制

当在 [`View`](xref:Xamarin.Forms.View) 的导航栏中显示 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 时，需要注意一些限制：

- 在 iOS 中，放置在 `NavigationPage` 导航栏中的视图会根据是否启用大标题显示在不同的位置。 有关启用大标题的详细信息，请参阅[显示大标题](~/xamarin-forms/platform/ios/page-large-title.md)。
- 在 Android 上，只有在使用 app-compat 的应用程序中，才能在 `NavigationPage` 的导航栏中放置视图。
- 不建议在 [ 的导航条中放置大而复杂的视图，如 `ListView`](xref:Xamarin.Forms.ListView)[ 和 `TableView`](xref:Xamarin.Forms.TableView)`NavigationPage`。

## <a name="related-links"></a>相关链接

- [页面导航](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [分层（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [如何在 Xamarin.Forms 视频中创建登录屏幕流](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
