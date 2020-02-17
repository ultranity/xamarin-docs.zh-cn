---
title: Xamarin.Forms 双屏设计模式
description: 本指南介绍了 Xamarin.Forms 如何支持已针对双屏设备进行优化的各种不同设计模式。
ms.prod: xamarin
ms.assetid: 3176d792-6dba-4e00-b463-497c58678ee9
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: c739b32cb45c27bbbc166f898dee964a7cbbbce6
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145521"
---
# <a name="xamarinforms-dual-screen-design-patterns"></a>Xamarin.Forms 双屏设计模式

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

本指南介绍了我们为双屏设备推荐的设计模式，还有代码和示例来帮助你创建可提供工程和实用用户体验的界面。

## <a name="extended-canvas-pattern"></a>延展画布模式

延展画布模式将两个屏幕看作一个大的画布，用来显示地图、图像、电子表格或其他因延展以占用最大空间而受益的类似内容。

![](design-patterns-images/extended-canvas-sample.png "Extended canvas sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:d="http://xamarin.com/schemas/2014/forms/design"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    x:Class="Xamarin.Duo.Forms.Samples.ExtendCanvas">
    <ContentPage.Content>
        <Grid>
            <WebView x:Name="webView" HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
            <SearchBar x:Name="searchBar" Placeholder="Find a place..." BackgroundColor="DarkGray" Opacity="0.8" HorizontalOptions="FillAndExpand" VerticalOptions="Start" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

在本例中， 无论是在一个屏幕中显示，还是横跨两个屏幕，`Grid` 和内部内容都将延展以占用屏幕的所有可用空间。

## <a name="master-detail-pattern"></a>主从模式

主从模式是指主视图（通常是左侧的一个列表）提供内容，用户从中选择项目，在右侧查看该项目的详细信息。

![](design-patterns-images/master-detail-sample.png "Master detail sample")

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.MasterDetail">
    <dualScreen:TwoPaneView MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualScreen:TwoPaneView.Pane1>
            <local:Master x:Name="masterPage"></local:Master>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:Details x:Name="detailsPage"></local:Details>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

在此示例中，你可使用 `TwoPaneView` 在一个窗格中设置一个列表，在另一个窗格中设置一个从视图。

## <a name="two-page-pattern"></a>双页模式

双页模式非常适合使自身采用双列布局，例如文档阅读器、笔记或艺术板。

![](design-patterns-images/two-page-sample.png "Two page sample")

```xaml
<Grid x:Name="layout">
    <CollectionView x:Name="cv" BackgroundColor="LightGray">
        <CollectionView.ItemsLayout>
            <GridItemsLayout
                SnapPointsAlignment="Start"
                SnapPointsType="MandatorySingle"
                Orientation="Horizontal"
                HorizontalItemSpacing="{Binding Source={x:Reference mainPage}, Path=HingeWidth}" />
        </CollectionView.ItemsLayout>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                        WidthRequest="{Binding Source={x:Reference mainPage}, Path=ContentWidth}"
                        HeightRequest="{Binding Source={x:Reference mainPage}, Path=ContentHeight}">
                    <Frame Margin="20" BackgroundColor="White">
                        <Label FontSize="Large" Text="{Binding .}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                    </Frame>
                </Frame>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</Grid>
```

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有在铰链宽度上拆分的网格布局，它是提供这一双屏体验的理想方式。

## <a name="dual-view-pattern"></a>双视图模式

双视图看起来可能与“双页”视图类似，区别在于内容和用户场景。 在此模式下，你并排比较内容的目的可能是编辑文档或照片、比较不同的餐厅菜单，或者区分代码文件的合并冲突。

![](design-patterns-images/dual-view-sample.png "Dual view sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.DualViewListPage">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <CollectionView SelectionMode="Single" x:Name="mapList">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Grid Padding="10,5,10,5">
                            <Frame Visual="Material" BorderColor="LightGray">
                                <StackLayout Padding="5">
                                    <Label FontSize="Title" Text="{Binding Title}"></Label>
                                </StackLayout>
                            </Frame>
                        </Grid>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:DualViewMap x:Name="mapPage"></local:DualViewMap>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="companion-pattern"></a>伴随模式

伴随模式演示了你可如何使用第二个屏幕来提供与主视图相关的第二层内容，例如在绘制应用、游戏或媒体编辑的情况下。

![](design-patterns-images/companion-pane-sample.png "Companion pane sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples" xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualscreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Name="mainPage"
    x:Class="Xamarin.Duo.Forms.Samples.CompanionPane"
    BackgroundColor="LightGray"
    Visual="Material">
    <dualscreen:TwoPaneView x:Name="twoPaneView" MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualscreen:TwoPaneView.Pane1>
            <CarouselView x:Name="cv" BackgroundColor="LightGray" IsScrollAnimated="False" >
                <CarouselView.ItemTemplate>
                    <DataTemplate>
                        <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                           WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Width}"
                           HeightRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Height}">
                            <Frame Margin="20" BackgroundColor="White">
                                <Label FontSize="Large" Text="{Binding ., StringFormat='Slide Content {0}'}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                            </Frame>
                        </Frame>
                    </DataTemplate>
                </CarouselView.ItemTemplate>
            </CarouselView>
        </dualscreen:TwoPaneView.Pane1>
        <dualscreen:TwoPaneView.Pane2>
            <CollectionView x:Name="indicators"
            SelectionMode="Single"
            Margin="20, 20, 20, 20"
            BackgroundColor="LightGray"
            WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
            ItemsSource="{Binding Source={x:Reference cv}, Path=ItemsSource}">
                <CollectionView.Resources>
                    <ResourceDictionary>
                        <Style TargetType="Frame">
                            <Setter Property="VisualStateManager.VisualStateGroups">
                                <VisualStateGroupList>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualState x:Name="Normal">
                                            <VisualState.Setters>
                                                <Setter Property="Padding" Value="0"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                        <VisualState x:Name="Selected">
                                            <VisualState.Setters>
                                                <Setter Property="BorderColor" Value="Green" />
                                                <Setter Property="Padding" Value="1"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateGroupList>
                            </Setter>
                        </Style>
                    </ResourceDictionary>
                </CollectionView.Resources>
                <CollectionView.ItemsLayout>
                    <LinearItemsLayout Orientation="Vertical" ItemSpacing="10"></LinearItemsLayout>
                </CollectionView.ItemsLayout>
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Frame WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}" CornerRadius="10" HeightRequest="60" BackgroundColor="White" Margin="0">
                            <StackLayout HorizontalOptions="Fill" VerticalOptions="Fill"  Orientation="Horizontal">
                                <Label FontSize="Micro" Padding="20,0,20,0" VerticalTextAlignment="Center" WidthRequest="140" Text="{Binding ., StringFormat='Slide Content {0}'}"></Label>
                                <Label FontSize="Small" Padding="20,0,20,0" VerticalTextAlignment="Center" HorizontalOptions="FillAndExpand" BackgroundColor="DarkGray"  Grid.Column="1" Text="{Binding ., StringFormat='Slide {0}'}"></Label>
                            </StackLayout>
                        </Frame>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualscreen:TwoPaneView.Pane2>
    </dualscreen:TwoPaneView>
</ContentPage>
```

## <a name="related-links"></a>相关链接

- [双屏示例 (GitHub)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
- [创建适合双屏设备的应用](index.md)
