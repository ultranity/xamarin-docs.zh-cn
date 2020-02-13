---
title: 自动化属性
description: 本文介绍如何在 Xamarin.Forms 应用程序中使用 AutomationProperties 类，以便屏幕阅读器可讲述页面上的元素。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 12c6229c1922f0bd4a4d25ca796bcb46141a326c
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131130"
---
# <a name="automation-properties-in-xamarinforms"></a>Xamarin.Forms 中的自动化属性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms 允许通过使用 AutomationProperties 类中的附加属性在用户界面元素上设置可访问性值，且后者反过来设置了本机可访问性值。本文介绍如何使用 AutomationProperties 类，以便屏幕阅读器可讲述页面上的元素。_

Xamarin.Forms 允许通过以下附加属性在用户界面元素上设置自动化属性：

- `AutomationProperties.IsInAccessibleTree` - 指示元素是否可用于可访问的应用程序。 有关详细信息，请参阅 [AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` - 元素的简短描述，用作元素的朗读标识符。 有关详细信息，请参阅 [AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` - 元素的详细说明，可以视为和元素相关的工具提示文本。 有关详细信息，请参阅 [AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` - 允许另一个元素定义当前元素的可访问性信息。 有关详细信息，请参阅 [AutomationProperties.LabeledBy](#labeledby)。

这些附加属性设置了本机可访问性值以便屏幕阅读器可讲述元素。 有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用 `AutomationProperties` 附加属性可能会影响 Android 上的 UI 测试执行。 [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)、`AutomationProperties.Name` 和 `AutomationProperties.HelpText` 属性都将设置本机 `ContentDescription` 属性，`AutomationProperties.Name` 和 `AutomationProperties.HelpText` 属性值都优先于 `AutomationId` 值（如果同时设置了 `AutomationProperties.Name` 和 `AutomationProperties.HelpText`则将连接这些值）。 这意味着，如果在元素上也设置了 `AutomationProperties.Name` 或 `AutomationProperties.HelpText`，则任何寻找 `AutomationId` 的测试都将失败。 在此方案中，应更改 UI 测试以查找 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 的值，或这两者的串联。

每个平台都有不同的屏幕读取器来叙述可访问性值：

- iOS 具有 VoiceOver。 有关详细信息，请参阅 developer.apple.com 上的[使用 VoiceOver 测试设备的可访问性](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)。
- Android 具有 TalkBack。 有关详细信息，请参阅 developer.android.com 上的[测试应用的可访问性](https://developer.android.com/training/accessibility/testing.html#talkback)。
- Windows 具有讲述人。 有关详细信息，请参阅[使用讲述人验证主要应用方案](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator)。

但是，屏幕阅读器的具体行为取决于软件和用户的配置。 例如，大多数屏幕阅读器在接收到焦点时读取与控件关联的文本，使用户在页面上的控件之间移动时能够自行定位。 某些屏幕阅读器在页面显示时也会读取整个应用程序的用户界面，使用户在尝试导航它之前能够收到所有页面上的可用信息内容。

屏幕阅读器也读取不同的可访问性值。 在示例应用程序中：

- VoiceOver 会读取 `Entry` 的 `Placeholder` 值，并提供使用该控件的说明。
- TalkBack 会读取 `Entry` 的 `Placeholder` 值，后跟 `AutomationProperties.HelpText` 值，并提供使用该控件的说明。
- 讲述人会读取 `Entry` 的 `AutomationProperties.LabeledBy` 值，并提供使用该控件的说明。

此外，讲述人将优先考虑 `AutomationProperties.Name``AutomationProperties.LabeledBy`，然后是 `AutomationProperties.HelpText`。 在 Android 中，TalkBack 可以合并 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 值。 因此，建议在每个平台上进行彻底的可访问性测试，以确保获得最佳体验。

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree` 附加属性是 `boolean`，它确定对于屏幕读取器而言元素是否可访问，是否因此可见。 它必须设置为 `true` 以使用其他可访问性附加属性。 这可以通过以下操作在 XAML 中实现：

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

或者，可在 C# 中设置如下：

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> 请注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法还可用于设置 `AutomationProperties.IsInAccessibleTree` 附加属性 - `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name` 附加属性值应为屏幕阅读器用于公告元素的简短的描述性文本字符串。 应为具有对理解内容或与用户界面交互重要意义的元素设置此属性。 这可以通过以下操作在 XAML 中实现：

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

或者，可在 C# 中设置如下：

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> 请注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法还可用于设置 `AutomationProperties.Name` 附加属性 - `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText` 附加属性应设置为描述用户界面元素的文本，且可视为与元素关联的工具提示文本。 这可以通过以下操作在 XAML 中实现：

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

或者，可在 C# 中设置如下：

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> 请注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法还可用于设置 `AutomationProperties.HelpText` 附加属性 - `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平台上，为了编辑控件例如 [`Entry`](xref:Xamarin.Forms.Entry)，有时可以省略 `HelpText` 属性并将其替换为占位符文本。 例如，“在此输入名字”是 [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性的一个很好的候选对象，它在用户实际输入之前将文本放在控件中。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy` 附加属性允许另一个元素定义当前元素的可访问性信息。 例如，[`Entry`](xref:Xamarin.Forms.Entry) 旁的 [`Label`](xref:Xamarin.Forms.Label) 可用于描述 `Entry` 所表示的内容。 这可以通过以下操作在 XAML 中实现：

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

或者，可在 C# 中设置如下：

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> 请注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法还可用于设置 `AutomationProperties.IsInAccessibleTree` 附加属性 - `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>辅助功能复杂性

以下部分将介绍在某些控件上设置辅助功能值的复杂性。

### <a name="navigationpage"></a>NavigationPage

在 Android 上，若要设置屏幕阅读器将为 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的操作栏中的后退箭头阅读的文本，请在 [`Page`](xref:Xamarin.Forms.Page) 上设置 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 属性。 但是，请注意，这将不会对 OS 后退按钮造成影响。

### <a name="masterdetailpage"></a>MasterDetailPage

在 iOS 和通用 Windows 平台 (UWP) 上，若要设置屏幕阅读器将为 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 上的切换按钮阅读的文本，请在 `MasterDetailPage` 上或在 `Master` 页面的 `IconImageSource` 属性上设置 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 属性。

在 Android 上，若要设置屏幕阅读器将为 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 上的切换按钮阅读的文本，请将字符串资源添加到此 Android 项目：

```xml
<resources>
    <string name="app_name">Xamarin Forms Control Gallery</string>
    <string name="btnMDPAutomationID_open">Open Side Menu message</string>
    <string name="btnMDPAutomationID_close">Close Side Menu message</string>
</resources>
```

然后将 `Master` 页面的 `IconImageSource` 属性的 `AutomationId` 属性设置为相应的字符串：

```csharp
var master = new ContentPage { ... };
master.IconImageSource.AutomationId = "btnMDPAutomationID";
```

### <a name="toolbaritem"></a>ToolbarItem

在 iOS、Android 和 UWP 上，屏幕阅读器将阅读 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 实例的 `Text` 属性值，条件是尚未定义 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 值。

在 iOS 和 UWP 上，`AutomationProperties.Name` 属性值将替换由屏幕阅读器阅读的 `Text` 属性值。

在 Android 上，`AutomationProperties.Name` 和/或 `AutomationProperties.HelpText` 属性值将完全替换可见且同时由屏幕阅读器阅读的 `Text` 属性值。 请注意，这是 API 少于 26 的一个限制。

## <a name="related-links"></a>相关链接

- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [可访问性（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
