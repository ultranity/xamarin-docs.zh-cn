---
title: Xamarin. Forms SwipeView
description: Xamarin SwipeView 是一个容器控件，该控件环绕一项内容，并提供通过轻扫手势显示的上下文菜单项。
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 6131287b200846a033e0c476d7039dfd774cab68
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635587"
---
# <a name="xamarinforms-swipeview"></a>Xamarin. Forms SwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView` 是一种容器控件，该控件环绕内容项，并提供通过轻扫手势显示的上下文菜单项：

[![IOS 和 Android 上的 CollectionView 中 SwipeView 的屏幕截图](swipeview-images/swipeview-collectionview.png "SwipeView 刷物品")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView 刷物品")

`SwipeView` 在 Xamarin. Forms 4.4 中提供。 但是，它当前是实验性的，只能通过将以下代码行添加到 iOS 上的 `AppDelegate` 类、Android 上的 `MainActivity` 类或 UWP 上的 `App` 类，然后再调用 `Forms.Init`：

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` 定义以下属性：

- `LeftItems`，类型 `SwipeItems`，表示在控件从左侧重击时可以调用的滑动项。
- `RightItems`，类型 `SwipeItems`，表示在控件从右侧重击时可以调用的滑动项。
- `TopItems`类型 `SwipeItems`，它表示可在从上到下重击控件时调用的滑动项。
- `BottomItems`，类型 `SwipeItems`，表示在控件从下到下重击时可以调用的滑动项。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标和样式。

此外，`SwipeView` 继承了[`ContentView`](xref:Xamarin.Forms.ContentView)类中的[`Content`](xref:Xamarin.Forms.ContentView.Content)属性。 `Content` 属性是 `SwipeView` 类的 content 属性，因此不需要显式设置。

`SwipeView` 类还定义四个事件：

- 当轻扫开始时，将激发 `SwipeStarted`。 此事件附带的 `SwipeStartedEventArgs` 对象具有类型 `SwipeDirection`的 `SwipeDirection` 属性。
- 当滑动移动时，将激发 `SwipeChanging`。 此事件附带的 `SwipeChangingEventArgs` 对象具有类型 `SwipeDirection`的 `SwipeDirection` 属性和类型 `double`的 `Offset` 属性。
- 当轻扫结束时，将激发 `SwipeEnded`。 此事件附带的 `SwipeEndedEventArgs` 对象具有类型 `SwipeDirection`的 `SwipeDirection` 属性。
- 当滑动项关闭时，将激发 `CloseRequested`。

此外，`SwipeView` 还定义了一个 `Close` 方法，该方法可关闭滑动项。

> [!NOTE]
> `SwipeView` 在 iOS 和 Android 上有特定于平台的，用于控制打开 `SwipeView`时所使用的转换。 有关详细信息，请参阅 SwipeView 上的["在 iOS 上滑动过渡模式](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md)" 和["SwipeView"](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md)。

## <a name="create-a-swipeview"></a>创建 SwipeView

`SwipeView` 必须定义 `SwipeView` 环绕的内容，并使用滑动手势显示的滑动项。 "轻扫" 项是放置在四个 `SwipeView` 方向集合之一中的一个或多个 `SwipeItem` 对象-`LeftItems`、`RightItems`、`TopItems`或 `BottomItems`。

下面的示例演示如何在 XAML 中实例化 `SwipeView`：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

等效 C# 代码如下：

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

在此示例中，`SwipeView` 内容是包含[`Label`](xref:Xamarin.Forms.Label)的[`Grid`](xref:Xamarin.Forms.Grid) ：

[![IOS 和 Android 上的 SwipeView 内容的屏幕截图](swipeview-images/swipeview-content.png "SwipeView 内容")](swipeview-images/swipeview-content-large.png#lightbox "SwipeView 内容")

滑动项用于对 `SwipeView` 内容执行操作，并在控件从左侧重击时显示：

[![IOS 和 Android 上的 SwipeView 刷卡器项的屏幕截图](swipeview-images/swipeview-swipeitems.png "SwipeView 刷物品")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 刷物品")

默认情况下，当用户点击一项时，将执行一项刷卡器项。 但是，可以更改这种行为。 有关详细信息，请参阅[扫 mode](#swipe-mode)。

执行某一轻扫项目后，将隐藏该滑动项，并重新显示 `SwipeView` 内容。 但是，可以更改这种行为。 有关详细信息，请参阅[轻扫行为](#swipe-behavior)。

> [!NOTE]
> 滑动内容和轻扫项目可以以内联方式放置或定义为资源。

## <a name="swipe-items"></a>刷物品

`LeftItems`、`RightItems`、`TopItems`和 `BottomItems` 集合都是 `SwipeItems`类型。 `SwipeItems` 类定义以下属性：

- `SwipeMode`类型的 `Mode`，指示滑动交互的效果。 有关轻扫模式的详细信息，请参阅[滑动模式](#swipe-mode)。
- `SwipeBehaviorOnInvoked`类型的 `SwipeBehaviorOnInvoked`，它指示在调用一项刷卡器项后 `SwipeView` 的行为方式。 有关轻扫行为的详细信息，请参阅[轻扫行为](#swipe-behavior)。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标和样式。

每个轻扫一项定义为一个 `SwipeItem` 对象，该对象放入四个 `SwipeItems` 方向集合之一。 `SwipeItem` 类派生自[`MenuItem`](xref:Xamarin.Forms.MenuItem)类，并添加以下成员：

- `Color`类型的 `BackgroundColor` 属性，用于定义轻扫项目的背景色。 此属性由可绑定的属性支持。
- `Invoked` 事件，该事件在执行刷卡器项时触发。

> [!IMPORTANT]
> [`MenuItem`](xref:Xamarin.Forms.MenuItem)类定义多个属性，包括 `Command`、`CommandParameter`、`IconImageSource`和 `Text`。 可以对 `SwipeItem` 对象设置这些属性，以定义其外观，并定义调用 "轻扫" 项时执行的 `ICommand`。 有关详细信息，请参阅[Xamarin。窗体 MenuItem](~/xamarin-forms/user-interface/menuitem.md)。

下面的示例演示 `SwipeView`的 `LeftItems` 集合中的两个 `SwipeItem` 对象：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

每个 `SwipeItem` 的外观由 `Text`、`IconImageSource`和 `BackgroundColor` 属性的组合定义：

[![IOS 和 Android 上的 SwipeView 刷卡器项的屏幕截图](swipeview-images/swipeview-swipeitems.png "SwipeView 刷物品")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 刷物品")

点击 `SwipeItem` 时，将激发其 `Invoked` 事件，并由其注册事件处理程序进行处理。 或者，可以将 `Command` 属性设置为在调用 `SwipeItem` 时将执行的 `ICommand` 实现。

> [!NOTE]
> 如果仅使用 `Text` 或 `IconImageSource` 属性定义 `SwipeItem` 的外观，则内容始终处于中心位置。

除了将 "轻扫" 项定义为 `SwipeItem` 对象外，还可以定义自定义的轻扫项目视图。 有关详细信息，请参阅[自定义滑动项](#custom-swipe-items)。

## <a name="swipe-direction"></a>滑动方向

`SwipeView` 支持四个不同的轻扫方向，并通过向其添加 `SwipeItem` 对象的方向 `SwipeItems` 集合定义滑动方向。 每个轻扫方向都可以容纳自己的滑动项。 例如，下面的示例演示了一个 `SwipeView`，其 "轻扫" 项取决于滑动方向：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

在此示例中，`SwipeView` 内容可以向右或向左重击。 向右轻扫将显示 "**删除**" "删除" 项，同时向左轻扫会显示 "**收藏**" 和 "**共享**" 轻扫项目。

> [!WARNING]
> 在 `SwipeView`上，一次只能设置一个方向 `SwipeItems` 集合的实例。 因此，`SwipeView`上不能有两个 `LeftItems` 定义。

`SwipeStarted`、`SwipeChanging`和 `SwipeEnded` 事件会通过事件参数中的 `SwipeDirection` 属性来报告滑动方向。 此属性的类型为 `SwipeDirection`，它是由四个成员组成的枚举：

- `Right` 指示发生了向右轻扫。
- `Left` 指示发生了左轻扫。
- `Up` 指示出现向上的扫。
- `Down` 指示发生向下轻扫。

## <a name="swipe-mode"></a>扫 mode 模式

`SwipeItems` 类具有一个 `Mode` 属性，该属性指示滑动交互的效果。 应将此属性设置为 `SwipeMode` 枚举成员之一：

- `Reveal` 指示轻扫可显示滑动的项目。 这是 `SwipeItems.Mode` 属性的默认值。
- `Execute` 指示轻扫会执行滑动项。

在 "显示模式" 中，用户 swipes 一个 `SwipeView` 打开一个由一个或多个 "轻扫" 项组成的菜单，并且必须显式点击 "轻扫" 项来执行。 执行 "轻扫" 项后，将关闭滑动项，并重新显示 `SwipeView` 内容。 在执行模式下，用户 swipes 一个 `SwipeView` 打开一个菜单，其中包含一个更轻扫的项，然后自动执行。 执行后，将关闭滑动项，并重新显示 `SwipeView` 内容。

以下示例显示了配置为使用执行模式的 `SwipeView`：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此示例中，`SwipeView` 内容可以重击地显示立即执行的轻扫项目。 执行后，将重新显示 `SwipeView` 内容。

## <a name="swipe-behavior"></a>轻扫行为

`SwipeItems` 类具有一个 `SwipeBehaviorOnInvoked` 属性，该属性指示在调用一项刷卡器项后 `SwipeView` 的行为方式。 应将此属性设置为 `SwipeBehaviorOnInvoked` 枚举成员之一：

- `Auto` 指示在显示模式下，`SwipeView` 在调用一项刷卡器后关闭，而在执行模式下，在调用一项刷卡器后，`SwipeView` 保持打开状态。 这是 `SwipeItems.SwipeBehaviorOnInvoked` 属性的默认值。
- `Close` 指示 `SwipeView` 在调用一项刷卡器后关闭。
- `RemainOpen` 指示调用一项刷卡器后 `SwipeView` 保持打开状态。

下面的示例演示如何将 `SwipeView` 配置为在调用一项刷卡器后保持打开状态：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>自定义滑动项

可以用 `SwipeItemView` 类型定义自定义滑动项。 `SwipeItemView` 类派生自[`ContentView`](xref:Xamarin.Forms.ContentView)类，并添加以下属性：

- `ICommand`类型的 `Command`，将在点击 "轻扫" 项时执行。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标和样式。

在执行 `Command` 后，`SwipeItemView` 类还定义在点击项时触发的 `Invoked` 事件。

下面的示例演示 `SwipeView`的 `LeftItems` 集合中的 `SwipeItemView` 对象：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此示例中，`SwipeItemView` 包含一个包含[`Entry`](xref:Xamarin.Forms.Entry)和[`Label`](xref:Xamarin.Forms.Label)的[`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 用户将输入输入到 `Entry`后，`SwipeViewItem` 的其余部分可用于执行 `SwipeItemView.Command` 属性所定义的 `ICommand`。

## <a name="disable-a-swipeview"></a>禁用 SwipeView

应用程序可能进入一种状态，在该状态下轻扫内容项不是有效操作。 在这种情况下，可以通过将 `SwipeView` 的 `IsEnabled` 属性设置为 `false`来禁用。 这会阻止用户刷内容以显示滑动项目。

此外，在定义 `SwipeItem` 或 `SwipeItemView`的 `Command` 属性时，可以指定 `ICommand` 的 `CanExecute` 委托，以启用或禁用滑动项。

## <a name="related-links"></a>相关链接

- [SwipeView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
