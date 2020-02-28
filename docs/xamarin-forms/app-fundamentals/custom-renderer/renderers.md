---
title: 呈现器基类和本机控件
description: 每个 Xamarin.Forms 控件都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 本文列出了用于实现每个 Xamarin.Forms 页面、布局、视图和单元的呈现器和本机控件类。
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: f4c2663e4e4e298975fc1e230750ec562547b0da
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636024"
---
# <a name="renderer-base-classes-and-native-controls"></a>呈现器基类和本机控件

“每个 Xamarin.Forms 控件都有一个附带的呈现器，适用于创建本机控件实例的各个平台。本文列出了用于实现每个 Xamarin.Forms 页面、布局、视图和单元的呈现器和本机控件类。” 

除 `MapRenderer` 类以外，可以在以下命名空间中找到特定于平台的呈现器：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer` 类位于以下命名空间：：

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Maps.UWP

> [!NOTE]
> 有关为 Shell 应用程序创建自定义呈现器的信息，请参阅 [Xamarin.Forms Shell 自定义呈现器](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)。

## <a name="pages"></a>Pages

下表列出实现每个 Xamarin.Forms [页面](~/xamarin-forms/user-interface/controls/pages.md)类型的呈现器和本机控件类：

|页面|呈现器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – 手机), TabletMasterDetailPageRenderer (iOS – 平板电脑), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (手机), UISplitViewController (平板电脑)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (自定义控件)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS 和 Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (自定义控件)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS 和 Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (透视)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>布局

下表列出实现每个 Xamarin.Forms [布局](~/xamarin-forms/user-interface/controls/layouts.md)类型的呈现器和本机控件类：

|布局|呈现器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|视图|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|视图|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|视图|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Border|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|视图|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|视图|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|视图|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|视图|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|视图|FrameworkElement|

## <a name="views"></a>视图

下表列出实现每个 Xamarin.Forms [视图](~/xamarin-forms/user-interface/controls/views.md)类型的呈现器和本机控件类：

|视图|呈现器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS 和 Android), BoxViewRenderer (UWP)|UIView|ViewGroup||矩形|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Button|AppCompatButton|Button|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||文本框|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||文本框|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||图像|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Button|
|`IndicatorView`|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VideoView|MediaElement|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|组合框|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|`RefreshView`|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||控件|
|`SwipeView`|SwipeViewRenderer|UIView||视图|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|开关|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS)、WebViewRenderer（Android 和 UWP）|WkWebView|WebView||WebView|

## <a name="cells"></a>单元

下表列出实现每个 Xamarin.Forms [单元](~/xamarin-forms/user-interface/controls/cells.md)类型的呈现器和本机控件类：

|单元|呈现器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|带有 UITextField 的 UITableViewCell|带有 TextView 和 EditText 的 LinearLayout|带有 TextBox 的 DataTemplate|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|带有 UISwitch 的 UITableViewCell|开关|带有网格且包含 TextBlock 和 ToggleSwitch 的 DataTemplate|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|带有两个 TextViews 的 LinearLayout|带有 StackPanel 且包含两个 Textblock 的 DataTemplate|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|带有 UIImage 的 UITableViewCell|带有两个 TextViews 和一个 ImageView 的 LinearLayout|带有网格且包含一个 Image 和两个 TextBlocks 的 DataTemplate|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|视图|带有 ContentPresenter 的 DataTemplate|

## <a name="summary"></a>总结

本文列出了用于实现每个 Xamarin.Forms 页面、布局、视图和单元的呈现器和本机控件类。 每个 Xamarin.Forms 控件都有一个附带的呈现器，适用于创建本机控件实例的各个平台。
