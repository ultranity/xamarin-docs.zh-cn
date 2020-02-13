---
title: 设计跨平台的 Xamarin.Forms 应用程序
description: 本文说明如何使用 XAML 样式为跨平台 Xamarin.Forms 应用程序设置样式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
ms.openlocfilehash: fbd957c68d7a9aa2f8e44c91fab6174d8ed72014
ms.sourcegitcommit: 5bcb6158693478ca1b3f6881dc912d3e7a8d1868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068730"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>为跨平台 Xamarin.Forms 应用程序设置样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

在本快速入门中，你将了解如何：

- 使用 XAML 样式为 Xamarin 应用程序设置样式。

本快速入门演练如何使用 XAML 样式为跨平台 Xamarin.Forms 应用程序设置样式。 最终的应用程序如下所示：

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>先决条件

在尝试本快速入门之前，应成功完成[上一个快速入门](database.md)。 或者，下载[上一个快速入门示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)并将它用作本快速入门的起点。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio 并打开 Notes 解决方案。

2. 在“解决方案资源管理器”的“Notes”项目中，双击“App.xaml”将其打开    。 然后将现有代码替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此代码定义一个 [`Thickness`](xref:Xamarin.Forms.Thickness) 值、一系列 [`Color`](xref:Xamarin.Forms.Color) 值以及用于 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的隐式样式。 请注意，可以在整个应用程序中使用这些样式（处于应用程序级 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中）。 有关 XAML 样式的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[样式](deepdive.md#styling)。

    通过按 Ctrl+S  ，保存对 App.xaml  所做的更改，然后关闭文件。

3. 在“解决方案资源管理器”的“Notes”项目中，双击“NotesPage.xaml”将其打开    。 然后将现有代码替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    此代码将 [`ListView`](xref:Xamarin.Forms.ListView) 的隐式样式添加到页面级 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，并将 `ListView.Margin` 属性设置为在应用程序级 `ResourceDictionary` 中定义的值。 请注意，`ListView` 隐式样式已添加到页面级 `ResourceDictionary`，因为它仅由 `NotesPage` 使用。 有关 XAML 样式的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[样式](deepdive.md#styling)。

    通过按 Ctrl+S  ，保存对 NotesPage.xaml  所做的更改，然后关闭文件。

4. 在“解决方案资源管理器”的“Notes”项目中，双击“NoteEntryPage.xaml”将其打开    。 然后将现有代码替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
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

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    此代码将 [`Editor`](xref:Xamarin.Forms.Editor) 和 [`Button`](xref:Xamarin.Forms.Button) 视图的隐式样式添加到页面级 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，并将 `StackLayout.Margin` 属性设置为在应用程序级 `ResourceDictionary` 中定义的值。 请注意，`Editor` 和 `Button` 隐式样式已添加到页面级 `ResourceDictionary`，因为它们仅由 `NoteEntryPage` 使用。 有关 XAML 样式的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[样式](deepdive.md#styling)。

    通过按 Ctrl+S  ，保存对 NoteEntryPage.xaml  所做的更改，然后关闭文件。

5. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    在 NotesPage  上，按 +  按钮以导航到 NoteEntryPage  并输入便笺。 在每个页面上，观察样式相对于上一个快速入门的变化情况。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac 并打开“Notes”项目。

2. 在“Solution Pad”的“Notes”项目中，双击“App.xaml”将其打开    。 然后将现有代码替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此代码定义一个 [`Thickness`](xref:Xamarin.Forms.Thickness) 值、一系列 [`Color`](xref:Xamarin.Forms.Color) 值以及用于 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的隐式样式。 请注意，可以在整个应用程序中使用这些样式（处于应用程序级 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中）。 有关 XAML 样式的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[样式](deepdive.md#styling)。

    通过选择“文件”>“保存”  ，或按 &#8984; + S  ，保存对 App.xaml  所做的更改，然后关闭文件。

3. 在“Solution Pad”的“Notes”项目中，双击“NotesPage.xaml”将其打开    。 然后将现有代码替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    此代码将 [`ListView`](xref:Xamarin.Forms.ListView) 的隐式样式添加到页面级 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，并将 `ListView.Margin` 属性设置为在应用程序级 `ResourceDictionary` 中定义的值。 请注意，`ListView` 隐式样式已添加到页面级 `ResourceDictionary`，因为它仅由 `NotesPage` 使用。 有关 XAML 样式的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[样式](deepdive.md#styling)。

    通过选择“文件”>“保存”  ，或按 &#8984; + S  ，保存对 NotesPage.xaml  所做的更改，然后关闭文件。

4. 在“Solution Pad”的“Notes”项目中，双击“NoteEntryPage.xaml”将其打开    。 然后将现有代码替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
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

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    此代码将 [`Editor`](xref:Xamarin.Forms.Editor) 和 [`Button`](xref:Xamarin.Forms.Button) 视图的隐式样式添加到页面级 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，并将 `StackLayout.Margin` 属性设置为在应用程序级 `ResourceDictionary` 中定义的值。 请注意，`Editor` 和 `Button` 隐式样式已添加到页面级 `ResourceDictionary`，因为它们仅由 `NoteEntryPage` 使用。 有关 XAML 样式的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[样式](deepdive.md#styling)。

    通过选择“文件”>“保存”  ，或按 &#8984; + S  ，保存对 NoteEntryPage.xaml  所做的更改，然后关闭文件。

5. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    在 NotesPage  上，按 +  按钮以导航到 NoteEntryPage  并输入便笺。 在每个页面上，观察样式相对于上一个快速入门的变化情况。

::: zone-end

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何：

- 使用 XAML 样式为 Xamarin 应用程序设置样式。

若要详细了解使用 Xamarin.Forms 进行应用程序开发的基础知识，请继续学习快速入门深入探讨。

> [!div class="nextstepaction"]
> [下一页](deepdive.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin.Forms 快速入门深入探讨](deepdive.md)
