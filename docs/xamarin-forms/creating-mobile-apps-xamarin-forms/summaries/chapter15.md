---
title: 第 15 章的摘要。 交互式接口
description: 使用 Xamarin.Forms 创建移动应用： 摘要的第 15 章。 交互式接口
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131104"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第 15 章的摘要。 交互式接口

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

本章探讨了八个 `View` 衍生产品，它们允许与用户交互。

## <a name="view-overview"></a>查看概述

Xamarin 包含20个可实例化的类，这些类派生自 `View` 而不是 `Layout`。 已在上一章节中讨论的这些六个：

- `Label`： [**第2章。应用解析**](chapter02.md)
- `BoxView`： [**第3章。滚动堆栈**](chapter03.md)
- `Button`： [**第6章。按钮单击**](chapter06.md)
- `Image`：第[ **13 章。位图**](chapter13.md)
- `ActivityIndicator`：第[ **13 章。位图**](chapter13.md)
- `ProgressBar`：第[ **14 章。AbsoluteLayout**](chapter14.md)

这一章中的八个视图有效地允许用户与基本.NET 数据类型进行交互：

|数据类型|视图|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry)、 [`Editor`](xref:Xamarin.Forms.Editor) [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

您可以将这些视图作为基础数据类型的可视交互式表示形式。 下一章第16章中更深入地探讨了此概念[ **。数据绑定**](chapter16.md)。

以下章节中介绍的剩余的六个视图：

- `WebView`：第[ **16 章。数据绑定**](chapter16.md)
- `Picker`：第[ **19 章。集合视图**](chapter19.md)
- `ListView`：第[ **19 章。集合视图**](chapter19.md)
- `TableView`：第[ **19 章。集合视图**](chapter19.md)
- `Map`：第[ **28 章。位置和地图**](chapter28.md)
- `OpenGLView`：本书未涵盖（并且不支持 Windows 平台）

## <a name="slider-and-stepper"></a>滑块和分档器

[`Slider`](xref:Xamarin.Forms.Slider)和[`Stepper`](xref:Xamarin.Forms.Stepper)都允许用户从范围中选择一个数值。 `Slider` 是连续范围，而 `Stepper` 涉及离散值。

### <a name="slider-basics"></a>滑块基础知识

[`Slider`](xref:Xamarin.Forms.Slider)是一个水平条，表示从最小值到右侧的最大值的范围。 它定义了三个公共属性：

- `double`类型的[`Value`](xref:Xamarin.Forms.Slider.Value) ，默认值为0
- `double`类型的[`Minimum`](xref:Xamarin.Forms.Slider.Minimum) ，默认值为0
- `double`类型的[`Maximum`](xref:Xamarin.Forms.Slider.Maximum) ，默认值为1

可绑定属性的支持这些属性，以确保它们一致：

- 对于所有三个属性，为可绑定属性指定的[`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate)方法确保 `Value` 介于 `Minimum` 和 `Maximum`之间。
- 如果 `Minimum` 设置为大于或等于 `Maximum`的值，则 `MinimumProperty` 上的[`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate)方法返回 `false`，与 `MaximumProperty`类似。 从 `validateValue` 方法返回 `false` 将导致引发 `ArgumentException`。

当 `Value` 属性更改时，`Slider` 以编程方式或在用户操作 `Slider`时，用[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)参数引发[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged)事件。

[**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)示例演示了 `Slider`的简单用法。

### <a name="common-pitfalls"></a>常见错误

在代码和 XAML 中，`Minimum` 和 `Maximum` 属性都按照您指定的顺序进行设置。 请确保初始化这些属性，以便 `Maximum` 始终大于 `Minimum`。 否则，将引发异常。

初始化 `Slider` 属性可能会导致 `Value` 属性发生更改，并激发 `ValueChanged` 事件。 应确保 `Slider` 事件处理程序不会访问在页初始化过程中尚未创建的视图。

`ValueChanged` 事件不会在 `Slider` 初始化期间激发，除非 `Value` 属性发生更改。 可以直接从代码调用 `ValueChanged` 处理程序。

### <a name="slider-color-selection"></a>滑块的颜色选择

[**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)程序包含三个 `Slider` 元素，它们允许您通过指定颜色的 RGB 值以交互方式选择颜色：

[![R G B 滑块的三向屏幕截图](images/ch15fg03-small.png "RGB 滑块")](images/ch15fg03-large.png#lightbox "RGB 滑块")

[**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)示例使用两个 `Slider` 元素跨 `AbsoluteLayout` 移动两个 `Label` 元素，并将一个元素淡入另一个。

### <a name="the-stepper-difference"></a>分档器差异

[`Stepper`](xref:Xamarin.Forms.Stepper)定义与 `Slider` 相同的属性和事件，但将 `Maximum` 属性初始化为100，并且 `Stepper` 定义第四个属性：

- 类型 `double`的[`Increment`](xref:Xamarin.Forms.Stepper.Increment) ，初始化为1

`Stepper` 以直观的方式包含标记 **&ndash;** 和 **+** 的两个按钮。 按 **&ndash;** 会将 `Value` `Increment` 降到最少 `Minimum`。 按 **+** 会 `Increment` 最多 `Maximum`增加 `Value`。

[**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)示例演示了这一点。

## <a name="switch-and-checkbox"></a>开关和复选框

[`Switch`](xref:Xamarin.Forms.Switch)允许用户指定布尔值。

### <a name="switch-basics"></a>切换基础知识

在视觉上，`Switch` 包括可以关闭和打开的切换。 类定义了一个属性：

- [ 类型的 `IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)`bool`

`Switch` 定义一个事件：

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)附带[`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs)对象，`IsToggled` 属性发生更改时激发。

[**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程序演示 `Switch`。

### <a name="a-traditional-checkbox"></a>传统的复选框

某些开发人员可能更倾向于 `Switch`的传统 `CheckBox`。 [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含从 `ContentView`派生的 `CheckBox` 类。 `CheckBox` 由[CheckBox](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)和[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)文件实现。 `CheckBox` 定义了三个属性（`Text`、`FontSize`和 `IsChecked`）和 `CheckedChanged` 事件。

[**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)示例演示了这一 `CheckBox`。

## <a name="typing-text"></a>键入文本

Xamarin.Forms 定义了三个视图，让用户输入和编辑文本：

- 为单行文本[`Entry`](xref:Xamarin.Forms.Entry)
- 为多行文本[`Editor`](xref:Xamarin.Forms.Editor)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)用于搜索的单行文本。

`Entry` 和 `Editor` 派生自[`InputView`](xref:Xamarin.Forms.InputView)，后者派生自 `View`。 `SearchBar` 直接从 `View`派生。

### <a name="keyboard-and-focus"></a>键盘和焦点

在无物理键盘的手机和平板电脑上，`Entry`、`Editor`和 `SearchBar` 元素都将导致弹出虚拟键盘。 此键盘在屏幕上存在相关输入焦点。 视图必须同时具有其[`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)和[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)属性设置为 "`true`" 才能获得输入焦点。

具有输入焦点所涉及的两种方法，一个只读的属性和两个事件。 这些都是由 `VisualElement`定义的：

- [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)方法尝试将输入焦点设置到元素，并在成功时返回 `true`
- [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)方法从元素中移除输入焦点
- [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)只读属性指示元素是否具有输入焦点
- [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)事件指示元素何时获得输入焦点
- [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件指示元素何时失去输入焦点

### <a name="choosing-the-keyboard"></a>选择键盘

`Entry` 和 `Editor` 派生的[`InputView`](xref:Xamarin.Forms.InputView)类只定义一个属性：

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 类型的 [`Keyboard`](xref:Xamarin.Forms.Keyboard)

此设置指示键盘显示的类型。 某些键盘进行了优化的 Uri 或数字。

`Keyboard` 类允许使用具有[`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)类型的参数的静态[`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags))方法（具有以下位标志的枚举）定义键盘：

- `None` 设置为0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)设置为1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)设置为2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)设置为4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)设置为 \xFFFFFFFF

当使用多行[`Editor`](xref:Xamarin.Forms.Editor)需要段落或更多文本时，调用 `Keyboard.Create` 是选择键盘的好方法。 对于单行[`Entry`](xref:Xamarin.Forms.Entry)，`Keyboard` 的以下静态只读属性很有用：

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)带有或不带小数点的正数。

[`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter)允许在 XAML 中按照[**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程序所示指定这些属性。

### <a name="entry-properties-and-events"></a>项属性和事件

单行[`Entry`](xref:Xamarin.Forms.Entry)定义以下属性：

- 类型 `string`[`Text`](xref:Xamarin.Forms.InputView.Text) ，在 `Entry` 中显示的文本
- [ 类型的 `TextColor`](xref:Xamarin.Forms.InputView.TextColor)`Color`
- [ 类型的 `FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)`string`
- [ 类型的 `FontSize`](xref:Xamarin.Forms.Entry.FontSize)`double`
- [ 类型的 `FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)`FontAttributes`
- `bool`类型的[`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) ，这会导致字符被屏蔽
- 类型 `string`的[`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) ，适用于在键入任何内容之前在 `Entry` 中显示的 dimly 彩色文本
- [ 类型的 `PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)`Color`

`Entry` 还定义了两个事件：

- 使用[`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)对象[`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) ，每当 `Text` 属性发生更改时触发
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)，在用户完成并关闭键盘时激发。 用户以特定于平台的方式指示完成

[**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)示例演示了这两个事件。

### <a name="the-editor-difference"></a>编辑器差异

多行[`Editor`](xref:Xamarin.Forms.Editor)定义与 `Entry` 相同的 `Text` 和 `Font` 属性，但不定义其他属性。 `Editor` 还定义了与 `Entry`相同的两个属性。

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是一个自由格式的注释，用于保存和还原 `Editor`的内容。

### <a name="the-searchbar"></a>SearchBar

[`SearchBar`](xref:Xamarin.Forms.SearchBar)不是派生自 `InputView`，因此它没有 `Keyboard` 属性。 但它确实具有 `Entry` 定义的所有 `Text`、`Font`和 `Placeholder` 属性。 此外，`SearchBar` 还定义了三个附加属性：

- [ 类型的 `CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)`Color`
- 与数据绑定和 MVVM 一起使用[`ICommand`](xref:System.Windows.Input.ICommand)类型[`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)
- `Object`类型的[`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) ，用于 `SearchCommand`

特定于平台的取消按钮擦除文本。 `SearchBar` 还具有特定于平台的搜索按钮。 按下任一按钮会引发 `SearchBar` 定义的两个事件之一：

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)附带[`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)对象
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)示例演示了 `SearchBar`。

## <a name="date-and-time-selection"></a>日期和时间选择

[`DatePicker`](xref:Xamarin.Forms.DatePicker)和[`TimePicker`](xref:Xamarin.Forms.TimePicker)视图实现了平台特定的控件，允许用户指定日期或时间。

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)定义四个属性：

- 类型 `DateTime`的[`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) ，初始化为1900年1月1日
- 类型 `DateTime`的[`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) ，初始化为2100年12月31日
- 类型 `DateTime`的[`Date`](xref:Xamarin.Forms.DatePicker.Date) ，已初始化为 `DateTime.Today`
- `string`类型的[`Format`](xref:Xamarin.Forms.DatePicker.Format) ，.net 格式设置字符串初始化为 "d"，即短日期模式，导致日期显示为美国的 "7/20/1969"。

可以通过将属性表达为属性元素并使用区域性固定的短日期格式（"7/20/1969"），在 XAML 中设置 `DateTime` 属性。   

[**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)示例计算用户选择的两个日期之间的天数。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>TimePicker （或其 TimeSpanPicker？）

[`TimePicker`](xref:Xamarin.Forms.TimePicker)定义了两个属性，而不定义任何事件：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)的类型 `TimeSpan` 而不是 `DateTime`，这表示从午夜开始所经过的时间。
- `string`类型的[`Format`](xref:Xamarin.Forms.TimePicker.Format) ，.net 格式设置字符串初始化为 "t"，这是短时间模式，导致时间显示，如美国的 "1:45 PM"。

[**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程序演示了如何使用 `TimePicker` 指定计时器的时间。 只有在将它保存在前台，仅适用于该程序。

**SetTimer**还演示了如何使用 `Page` 的[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))方法来显示一个警报框。

## <a name="related-links"></a>相关链接

- [第15章完整文本（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第15章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [滑块](~/xamarin-forms/user-interface/slider.md)
- [项](~/xamarin-forms/user-interface/text/entry.md)
- [编辑器](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
