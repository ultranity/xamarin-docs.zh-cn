---
title: XAML 控件
description: Xamarin 中定义的所有视图都可以从 XAML 文件引用。
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/14/2020
ms.openlocfilehash: c4d2c6bdab09e87bcfc4a191c64425316fe133f9
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635844"
---
# <a name="xaml-controls"></a>XAML 控件

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

视图是用户界面对象（如标签、按钮和滑块），这些对象通常称为*控件*或其他图形编程环境中的*小组件*。 Xamarin 支持的视图全都派生自[`View`](xref:Xamarin.Forms.View)类。

Xamarin 中定义的所有视图都可以从 XAML 文件引用。

## <a name="views-for-presentation"></a>用于演示的视图

|     |     |
| --- | --- |
| <h3>BoxView</h3>显示特定颜色的矩形。<p align="center">![BoxView 的屏幕截图](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [指南](~/xamarin-forms/user-interface/boxview.md) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>映像</h3>显示位图。<p align="center">![图像的屏幕截图](xaml-controls-images/Image.png "映像")</p>[API](xref:Xamarin.Forms.Image) / [指南](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Label</h3>显示一个或多个文本行。<p align="center">![标签屏幕截图](xaml-controls-images/Label.png "Label")</p>[API](xref:Xamarin.Forms.Label) / [指南](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>映射</h3>显示地图。<p align="center">![图的屏幕截图](xaml-controls-images/Map.png "映射")</p>[API](xref:Xamarin.Forms.Maps.Map) / [指南](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>播放视频或音频。<p align="center">![MediaElement 的屏幕截图](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement) / [指南](~/xamarin-forms/user-interface/mediaelement.md) | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>Web 视图</h3>显示网页或 HTML 内容。<p align="center">![Web 视图的屏幕截图](xaml-controls-images/WebView.png "Web 视图")</p>[API](xref:Xamarin.Forms.WebView) / [指南](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>启动命令的视图

|     |     |
| --- | --- |
| <h3>按钮</h3>显示矩形对象中的文本。<p align="center">![按钮的屏幕截图](xaml-controls-images/Button.png "按钮")</p>[API](xref:Xamarin.Forms.Button) / [指南](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>在矩形对象中显示图像。<p align="center">![ImageButton 的屏幕截图](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [指南](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RefreshView</h3>提供可滚动内容的请求刷新功能。<p align="center">![RefreshView 的屏幕截图](xaml-controls-images/RefreshView.png "RefreshView")</p>[向导](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>搜索栏</h3> 接受用于执行搜索的用户输入。<p align="center">![SearchBar 的屏幕截图](xaml-controls-images/SearchBar.png "搜索栏")</p>[向导](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder=&quot;Enter search term&quot;<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> 提供通过轻扫手势显示的上下文菜单项。<p align="center">![SwipeView 的屏幕截图](xaml-controls-images/SwipeView.png "SwipeView")</p>[向导](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>设置值的视图

|     |     |
| --- | --- |
| <h3>CheckBox</h3>允许选择 `boolean` 值。<p align="center">![复选框的屏幕截图](xaml-controls-images/CheckBox.png "CheckBox")</p> [向导](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>滑块</h3>允许从连续范围中选择 `double` 值。<p align="center">![滚动条的屏幕截图](xaml-controls-images/Slider.png "滑块")</p>[API](xref:Xamarin.Forms.Slider) / [指南](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>步进器</h3>允许从增量范围中选择 `double` 值。<p align="center">![分档器的屏幕截图](xaml-controls-images/Stepper.png "步进器")</p>[API](xref:Xamarin.Forms.Stepper) / [指南](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>开关</h3>允许选择 `boolean` 值。<p align="center">![交换机屏幕截图](xaml-controls-images/Switch.png "开关")</p>[API](xref:Xamarin.Forms.Switch) / [指南](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>允许选择日期。<p align="center">![DatePicker 的屏幕截图](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [指南](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>允许选择一段时间。<p align="center">![TimePicker 的屏幕截图](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [指南](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>用于编辑文本的视图

|     |     |
| --- | --- |
| <h3>条目</h3>允许输入和编辑单个文本行。<p align="center">![条目的屏幕截图](xaml-controls-images/Entry.png "条目")</p>[API](xref:Xamarin.Forms.Entry) / [指南](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>编辑器</h3>允许输入和编辑多行文本。<p align="center">![编辑器的屏幕截图](xaml-controls-images/Editor.png "Label")</p>[API](xref:Xamarin.Forms.Editor) / [指南](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>指示活动的视图

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>显示一个动画来表明，应用程序参与了长时间的活动，而不提供任何进度指示。<p align="center">![ActivityIndicator 的屏幕截图](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) / [指南](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>进度条</h3>显示一个动画，用于显示应用程序正在经历长时间的活动。<p align="center">![ProgressBar 的屏幕截图](xaml-controls-images/ProgressBar.png "进度条")</p>[API](xref:Xamarin.Forms.ProgressBar) / [指南](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>显示集合的视图

|     |     |
| --- | --- |
| <h3>CarouselView</h3>显示一个可滚动的数据项列表。<p align="center">![CarouselView 的屏幕截图](xaml-controls-images/CarouselView.png "CarouselView")</p>[向导](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>使用不同的布局规范显示可滚动的数据项列表。<p align="center">![CollectionView 的屏幕截图](xaml-controls-images/CollectionView.png "CollectionView")</p>[向导](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>IndicatorView</h3>显示表示 `CarouselView`中的项数的指示器。<p align="center">![IndicatorView 的屏幕截图](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[向导](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView IndicatorView.ItemsSourceBy="carouselView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>显示可选择数据项的可滚动列表。<p align="center">![ListView 的屏幕截图](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [指南](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>选取器</h3>显示文本字符串列表中的选择项。<p align="center">![选取器的屏幕截图](xaml-controls-images/Picker.png "选取器")</p>[API](xref:Xamarin.Forms.Picker) / [指南](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>显示交互式行的列表。<p align="center">![TableView 的屏幕截图](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [指南](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
