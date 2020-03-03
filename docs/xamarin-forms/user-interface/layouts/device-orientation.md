---
title: 设备方向
description: 此文章介绍了如何查找在纵向和横向方向上出色的布局 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: f7526b6cecebadd30e95718b7e537026a6557adf
ms.sourcegitcommit: f43d5ecafd19cbc5cce39201916a83927a34617a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78214991"
---
# <a name="device-orientation"></a>设备方向

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

请务必要考虑将如何使用你的应用程序和如何合并横向方向以改善用户体验。 可以设计单独的布局以容纳多个方向和最佳使用的可用空间。 在应用程序级别，可以禁用或启用旋转。

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>控制方向

使用 Xamarin.Forms 时, 控制设备方向的受支持的方法是为每个单独的项目使用的设置。

### <a name="ios"></a>iOS

在 iOS 上，使用**info.plist**文件为应用程序配置设备方向。 如果该应用包含它作为目标，此文件将包含的 iPhone 和 iPod，方向设置，以及适用于 iPad 的设置。 以下是对 IDE 的特定说明。 在本文档的顶部使用 IDE 选项选择你想要看到的说明进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，打开 iOS 项目并打开**info.plist**。 该文件将打开到配置面板中，从 iPhone 部署信息选项卡开始：

![iPhone Visual Studio 中的部署信息](device-orientation-images/orientation-vs-iphone.png)

若要配置 iPad 方向，请选择面板左上角的 " **Ipad 部署信息**" 选项卡，并从可用方向中进行选择：

![在 Visual Studio 中支持的设备方向](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，打开 iOS 项目并打开**info.plist**。 在 "**应用程序**" 选项卡下，可以使用以下各节设置方向：

![iPhone 部署信息在 Visual Studio for Mac](device-orientation-images/orientation-xam-ui.png)

如果希望使用键值编辑器界面编辑值，请选择屏幕底部的 "**源**>" 选项卡：

![在 Visual Studio for Mac 支持设备方向](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

若要控制 Android 方向，请打开**MainActivity.cs** ，并使用修饰 `MainActivity` 类的属性设置方向：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android 支持多个选项用于指定方向：

- 无论传感器数据如何，**横向**&ndash; 都可强制应用程序方向为横向。
- **纵向**&ndash; 强制应用程序方向为纵向，而不考虑传感器数据。
- **用户**&ndash; 会导致使用用户首选的方向呈现应用程序。
- &ndash;**后**，应用程序的方向与其后[活动](xref:Android.App.Activity)的方向相同。
- **传感器**&ndash; 会使应用程序的方向由传感器确定，即使用户已禁用自动旋转也是如此。
- **SensorLandscape** &ndash; 会导致应用程序在使用传感器数据时使用横向方向，以更改屏幕的方向（以使屏幕不会出现倒置）。
- 当使用传感器数据更改屏幕的方向时， **SensorPortrait** &ndash; 会导致应用程序使用纵向（以便屏幕看起来不到倒置）。
- **ReverseLandscape** &ndash; 会使应用程序使用横向方向，使其与普通方向相对相反，因此显示 "倒置"。
- **ReversePortrait** &ndash; 会使应用程序使用纵向方向，使其与平时方向相反，因此显示 "倒置"。
- **FullSensor** &ndash; 会使应用程序依赖传感器数据来选择正确的方向（不可能为4个）。
- **FullUser** &ndash; 会使应用程序使用用户的方向首选项。 如果启用自动旋转，则可以使用所有 4 个方向。
- **UserLandscape** &ndash; _\[不受支持\]_ 会使应用程序使用横向方向，除非用户启用了自动旋转，在这种情况下，它将使用传感器来确定方向。 此选项将中断编译。
- **UserPortrait** &ndash; _\[不受支持\]_ 会使应用程序使用纵向，除非用户启用了自动旋转，在这种情况下，它将使用传感器来确定方向。 此选项将中断编译。
- **锁定**&ndash; _\[不受支持\]_ 会使应用程序在启动时使用屏幕方向，而不会响应设备物理方向的变化。 此选项将中断编译。

请注意，本机 Android Api 提供了很多方向的管理方式的控制，包括显式与用户相矛盾的选项以表示首选项。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台（UWP）上，支持的方向是在**appxmanifest.xml**文件中设置的。 打开清单，将显示在其中选择支持的方向配置面板。

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>对方向中的更改作出反应

Xamarin.Forms 不提供任何本机事件用于通知的共享代码中的方向更改您的应用程序。 但是，[Xamarin](~/essentials/index.md)包含一个提供方向更改通知的 [`DeviceDisplay`] 类。

若要检测没有 Xamarin 的方向，请监视 `Page`的 `SizeChanged` 事件，此事件在 `Page` 的宽度或高度发生更改时激发。 当 `Page` 的宽度大于高度时，设备处于横向模式。 有关详细信息，请参阅[基于屏幕方向显示图像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)。

或者，可以覆盖 `Page`上的[`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*)方法，并在其中插入任何布局更改逻辑。 只要 `Page` 分配新大小，就会调用 `OnSizeAllocated` 方法，只要设备旋转就会发生这种情况。 请注意，`OnSizeAllocated` 的基实现执行重要的布局功能，因此在重写中调用基实现非常重要：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

未能执行该步骤将导致非正常运行的页面。

请注意，在旋转设备时，可能会多次调用 `OnSizeAllocated` 方法。 每次更改你的布局是一种浪费的资源，并可能会导致闪烁。 请考虑使用在页面内的一个实例变量跟踪是否方向为横向或纵向，并只重绘更改时：

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

后检测到在设备方向更改，你可能想要添加或删除与用户界面的可用空间的更改做出反应的其他视图。 例如，考虑纵向时的每个平台上内置的计算器：

![](device-orientation-images/calculator-portrait.png "Calculator Application in Portrait")

和横向：

![](device-orientation-images/calculator-landscape.png "Calculator Application in Landscape")

请注意，应用程序通过添加更多的功能在环境中充分利用可用空间。

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>响应式布局

就可以使用内置的布局，以便适当地过渡旋转设备时的设计界面。 设计将继续响应方向中的更改时很吸引人的接口时请考虑以下一般规则：

- 请**注意，** 方向 &ndash; 变化的变化可能会导致在对比率产生某些假设时出现问题。 例如，一个视图，它会在 1/3 的纵向时的屏幕的垂直空间中具有足够的空间可能无法放入 1/3 的环境中的垂直空间。
- 请**注意绝对值**&ndash; 绝对值（像素）值，这些值在人像上非常有意义。 如果绝对值是必需的使用嵌套的布局隔离它们的影响。 例如，如果项模板具有有保证的统一高度，则可以在 `TableView` `ItemTemplate` 中使用绝对值。

上述规则同样适用时通常实现接口，用于多个屏幕大小以及被视为最佳做法。 本指南的其余部分将介绍在 Xamarin.Forms 中使用上述每种主要布局的响应式布局的具体示例。

> [!NOTE]
> 为清楚起见，以下部分演示了如何一次只使用一种类型的 `Layout` 实现响应式布局。 在实践中，混用 `Layout`来使用每个组件的更简单或最直观的 `Layout` 来实现所需的布局通常更为简单。

### <a name="stacklayout"></a>StackLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-stack-portrait.png "Photo Application in Portrait")

和横向：

![](device-orientation-images/photo-stack-landscape.png "Photo Application in Landscape")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

某些C#用于根据设备方向更改 `outerStack` 的方向：

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

注意以下各项：

- 调整 `outerStack` 以根据方向将图像和控件呈现为水平或垂直堆栈，以最好地利用可用空间。

### <a name="absolutelayout"></a>AbsoluteLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-abs-portrait.png "Photo Application in Portrait")

和横向：

![](device-orientation-images/photo-abs-landscape.png "Photo Application in Landscape")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

注意以下各项：

- 由于页面布局的方式，不是需要程序代码引入响应能力。
- 该 `ScrollView` 用于允许标签可见，即使屏幕的高度小于按钮和图像的固定高度的和也是如此。

### <a name="relativelayout"></a>RelativeLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-rel-portrait.png "Photo Application in Portrait")

和横向：

![](device-orientation-images/photo-rel-landscape.png "Photo Application in Landscape")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

注意以下各项：

- 由于页面布局的方式，不是需要程序代码引入响应能力。
- 该 `ScrollView` 用于允许标签可见，即使屏幕的高度小于按钮和图像的固定高度的和也是如此。

### <a name="grid"></a>网格

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-grid-portrait.png "Photo Application in Portrait")

和横向：

![](device-orientation-images/photo-grid-landscape.png "Photo Application in Landscape")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以下过程与代码一起处理旋转更改：

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

注意以下各项：

- 页面布局的方式，因此没有方法来更改网格放置控件。

## <a name="related-links"></a>相关链接

- [布局（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [响应式布局（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [基于屏幕方向显示图像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
