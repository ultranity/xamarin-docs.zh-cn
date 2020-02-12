---
title: Xamarin.Forms 编辑器
description: 本文介绍如何使用 Xamarin.Forms 编辑器控件接受的应用程序中的多行文本输入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 1ae176cfebdde31038c30895d1bf562ff3396eaa
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131071"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms 编辑器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_多行文本输入_

[`Editor`](xref:Xamarin.Forms.Editor)控件用于接受多行输入。 本文介绍：

- **[自定义](#customization)** &ndash; 键盘和颜色选项。
- **[交互](#interactivity)** &ndash; 可侦听以提供交互性的事件。

## <a name="customization"></a>自定义

### <a name="setting-and-reading-text"></a>设置和读取文本

与其他文本显示视图一样， [`Editor`](xref:Xamarin.Forms.Editor)公开 `Text` 属性。 此属性可用于设置和读取 `Editor`显示的文本。 下面的示例演示如何在 XAML 中设置 `Text` 属性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要读取文本，请访问中C#的 `Text` 属性：

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>设置占位符文本

[`Editor`](xref:Xamarin.Forms.Editor)可以设置为在不存储用户输入时显示占位符文本。 这是通过将[`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)属性设置为 `string`来实现的，它通常用于指示适用于 `Editor`的内容类型。 此外，可以通过将[`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)属性设置为[`Color`](xref:Xamarin.Forms.Color)来控制占位符文本颜色：

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>防止文本输入

可以通过将默认值为 `false`的 `IsReadOnly` 属性设置为 `true`，阻止用户修改[`Editor`](xref:Xamarin.Forms.Editor)中的文本：

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly` 属性不会改变[`Editor`](xref:Xamarin.Forms.Editor)的视觉外观，这与同时将 `Editor` 的视觉外观更改为灰色的 `IsEnabled` 属性不同。

### <a name="limiting-input-length"></a>输入的长度限制

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制[`Editor`](xref:Xamarin.Forms.Editor)允许的输入长度。 此属性应设置为一个正整数：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

如果[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)属性值为0，则表示不允许任何输入，值为 `int.MaxValue`（这是[`Editor`](xref:Xamarin.Forms.Editor)的默认值）指示对于可输入的字符数没有有效限制。

### <a name="character-spacing"></a>字符间距

通过将 `Editor.CharacterSpacing` 属性设置为 `double` 值，可将字符间距应用到[`Editor`](xref:Xamarin.Forms.Editor) ：

```xaml
<Editor ...
        CharacterSpacing="10" />
```

等效 C# 代码如下：

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

结果是[`Editor`](xref:Xamarin.Forms.Editor)显示的文本中的字符间距 `CharacterSpacing` 与设备无关的单位。

> [!NOTE]
> `CharacterSpacing` 属性值将应用于 `Text` 和 `Placeholder` 属性显示的文本。

### <a name="auto-sizing-an-editor"></a>自动调整大小编辑器

可以通过将 " [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) " 属性设置为 " [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)" 来自动调整其内容的[`Editor`](xref:Xamarin.Forms.Editor) ，这是[`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption)枚举的值。 此枚举有两个值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled)指示禁用自动调整大小，并为默认值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)指示启用自动调整大小。

这可以在代码中完成，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

启用自动调整大小后，当用户用文本填充它时， [`Editor`](xref:Xamarin.Forms.Editor)的高度将增加，而当用户删除文本时，高度将降低。

> [!NOTE]
> 如果已设置了[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)属性，则[`Editor`](xref:Xamarin.Forms.Editor)不会自动调整大小。

### <a name="customizing-the-keyboard"></a>自定义键盘

用户与[`Editor`](xref:Xamarin.Forms.Editor)交互时显示的键盘可以通过[`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)属性以编程方式设置为[`Keyboard`](xref:Xamarin.Forms.Keyboard)类中的以下属性之一：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – 用于短信和表情符号有用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – 默认键盘。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – 输入电子邮件地址时使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – 输入数字时使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – 输入文本时使用，而无需指定任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – 输入电话号码时使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – 输入文本时使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – 用于输入文件路径和 Web 地址。

这可以通过以下操作在 XAML 中实现：

```xaml
<Editor Keyboard="Chat" />
```

等效 C# 代码如下：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

每个键盘的示例可在我们的[食谱](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry)存储库中找到。

[`Keyboard`](xref:Xamarin.Forms.Keyboard) 类还具有 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) 工厂方法，可用于通过指定大小写、拼写检查和建议行为来自定义键盘。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 枚举值指定为方法的参数，并返回自定义的 `Keyboard`。 `KeyboardFlags` 枚举包含以下值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – 没有功能添加到键盘。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – 指示输入的每句话的第一个词的首字母将自动大写。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – 指示将对输入的文本执行拼写检查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – 指示将对输入的文本执行单词自动完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – 指示每个词的首字母将自动大写。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – 指示每个字符将自动大写。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – 指示不会发生自动大写。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – 指示将对输入的文本执行拼写检查、单词自动完成和句子首字母大写。

以下 XAML 代码示例演示如何自定义默认 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 来执行单词自动完成并将输入的每个字符的首字母大写：

```xaml
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

等效 C# 代码如下：

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否启用拼写检查。 默认情况下，属性设置为 `true`。 当用户输入文本，指示拼写错误。

但对于某些文本输入方案，如输入用户名，拼写检查会提供消极的体验，因此应通过将[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为 `false`来禁用：

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 如果[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为 `false`，并且未使用自定义键盘，则将禁用本机拼写检查器。 但是，如果已设置[`Keyboard`](xref:Xamarin.Forms.Keyboard)禁用拼写检查，如[`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat)，则将忽略 `IsSpellCheckEnabled` 属性。 因此，该属性不能用于对显式禁用该属性的 `Keyboard` 启用拼写检查。

### <a name="enabling-and-disabling-text-prediction"></a>启用和禁用文本预测

`IsTextPredictionEnabled` 属性控制是否启用文本预测和自动文本更正。 默认情况下，属性设置为 `true`。 当用户输入文本时，会显示 word 预测。

但是，对于某些文本输入方案，例如输入用户名，文本预测和自动文本更正将提供消极的体验，并应通过将 `IsTextPredictionEnabled` 属性设置为 `false`来禁用：

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 如果将 `IsTextPredictionEnabled` 属性设置为 `false`，并且未使用自定义键盘，则会禁用文本预测和自动文本更正。 但是，如果已设置了禁用文本预测的[`Keyboard`](xref:Xamarin.Forms.Keyboard) ，则将忽略 `IsTextPredictionEnabled` 属性。 因此，该属性不能用于对显式禁用它的 `Keyboard` 启用文本预测。

### <a name="colors"></a>颜色

可以通过 `BackgroundColor` 属性将 `Editor` 设置为使用自定义背景色。 特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的文本颜色的默认值，可能需要设置每个平台的自定义背景颜色。 有关优化每个平台的 UI 的详细信息，请参阅[使用平台](~/xamarin-forms/platform/device.md)调整程序。

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Editor with BackgroundColor Example")

请确保你选择的背景和文本颜色为每个平台上可用，且变得模糊的任何占位符文本。

## <a name="interactivity"></a>交互

`Editor` 公开两个事件：

- 当文本在编辑器中发生更改时， [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; 引发。 更改之前和之后提供的文本。
- [完成](xref:Xamarin.Forms.Editor.Completed)&ndash; 在用户通过按键盘上的 return 键结束输入时引发。

> [!NOTE]
> [`Entry`](xref:Xamarin.Forms.Entry)从中继承的[`VisualElement`](xref:Xamarin.Forms.VisualElement)类也具有[`Focused`](xref:Xamarin.Forms.VisualElement.Focused)和[`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件。

### <a name="completed"></a>已完成

`Completed` 事件用于响应与 `Editor`交互的完成。 如果用户通过输入键盘上的 return 键（或按 UWP 上的 Tab 键）来结束输入，则会引发 `Completed`。 事件的处理程序是一个泛型事件处理程序，用于获取发送方和 `EventArgs`：

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

可以在代码和 XAML 中订阅的已完成的事件：

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

`TextChanged` 事件用于响应字段内容的更改。

当 `Editor` 的 `Text` 更改时，将引发 `TextChanged`。 事件的处理程序采用 `TextChangedEventArgs`的实例。 `TextChangedEventArgs` 通过 `OldTextValue` 和 `NewTextValue` 属性提供对 `Editor` `Text` 的新旧值的访问权限：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

可以在代码和 XAML 中订阅的已完成的事件：

在代码中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

## <a name="related-links"></a>相关链接

- [文本（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [编辑器 API](xref:Xamarin.Forms.Editor)
