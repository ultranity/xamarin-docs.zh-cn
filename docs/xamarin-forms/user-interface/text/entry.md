---
title: Xamarin.Forms 条目
description: 此文章介绍了如何使用 Xamarin.Forms 入口类接受的单行文本或应用程序中的密码输入。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: c7daad8898d3048f10c9489ee4e2c78f147c6e52
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131150"
---
# <a name="xamarinforms-entry"></a>Xamarin.Forms 条目

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_单行文本或密码输入_

Xamarin. Forms [`Entry`](xref:Xamarin.Forms.Entry)用于单行文本输入。 `Entry`（如[`Editor`](xref:Xamarin.Forms.Editor)视图）支持多种键盘类型。 此外，`Entry` 可以用作密码字段。

## <a name="display-customization"></a>显示自定义

### <a name="setting-and-reading-text"></a>设置和读取文本

与其他文本显示视图一样，`Entry`公开[`Text`](xref:Xamarin.Forms.InputView.Text)属性。 此属性可用于设置和读取 `Entry`显示的文本。 下面的示例演示如何在 XAML 中设置 `Text` 属性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要读取文本，请访问中C#的 `Text` 属性：

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>设置占位符文本

[`Entry`](xref:Xamarin.Forms.Entry)可以设置为在不存储用户输入时显示占位符文本。 这是通过将[`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)属性设置为 `string`来实现的，它通常用于指示适用于 `Entry`的内容类型。 此外，可以通过将[`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)属性设置为[`Color`](xref:Xamarin.Forms.Color)来控制占位符文本颜色：

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> 可以通过设置其 `WidthRequest` 属性来定义 `Entry` 的宽度。 不依赖于根据其 `Text` 属性值定义的 `Entry` 的宽度。

### <a name="preventing-text-entry"></a>防止文本输入

可以通过将默认值为 `false`的 `IsReadOnly` 属性设置为 `true`，阻止用户修改[`Entry`](xref:Xamarin.Forms.Entry)中的文本：

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly` 属性不会改变[`Entry`](xref:Xamarin.Forms.Entry)的视觉外观，这与同时将 `Entry` 的视觉外观更改为灰色的 `IsEnabled` 属性不同。

### <a name="limiting-input-length"></a>输入的长度限制

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制[`Entry`](xref:Xamarin.Forms.Entry)允许的输入长度。 此属性应设置为一个正整数：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

如果[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)属性值为0，则表示不允许任何输入，值为 `int.MaxValue`（这是[`Entry`](xref:Xamarin.Forms.Entry)的默认值）指示对于可输入的字符数没有有效限制。

### <a name="character-spacing"></a>字符间距

通过将 `Entry.CharacterSpacing` 属性设置为 `double` 值，可将字符间距应用到[`Entry`](xref:Xamarin.Forms.Entry) ：

```xaml
<Entry ...
       CharacterSpacing="10" />
```

等效 C# 代码如下：

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

结果是[`Entry`](xref:Xamarin.Forms.Entry)显示的文本中的字符间距 `CharacterSpacing` 与设备无关的单位。

> [!NOTE]
> `CharacterSpacing` 属性值将应用于 `Text` 和 `Placeholder` 属性显示的文本。

### <a name="password-fields"></a>密码字段

`Entry` 提供 `IsPassword` 属性。 `true``IsPassword` 时，字段的内容将显示为黑色圆圈：

在 XAML:

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Entry IsPassword 示例](entry-images/password.png)

占位符可以与配置为密码字段 `Entry` 的实例一起使用：

在 XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Entry IsPassword 和 Placeholder 示例](entry-images/passwordplaceholder.png)

### <a name="setting-the-cursor-position-and-text-selection-length"></a>设置光标位置和文本所选内容长度

[`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition)属性可用于返回或设置将下一个字符插入到[`Text`](xref:Xamarin.Forms.InputView.Text)属性中存储的字符串的位置：

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

[`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition)属性的默认值为0，这表示将在 `Entry`开头处插入文本。

此外， [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength)属性还可用于返回或设置 `Entry`中选定文本的长度：

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

[`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength)属性的默认值为0，表示未选择任何文本。

### <a name="displaying-a-clear-button"></a>显示 "清除" 按钮

`ClearButtonVisibility` 属性可用于控制[`Entry`](xref:Xamarin.Forms.Entry)是否显示 "清除" 按钮，以便用户可以清除文本。 应将此属性设置为 `ClearButtonVisibility` 枚举成员：

- `Never` 指示将永远不会显示 "清除" 按钮。 这是 `Entry.ClearButtonVisibility` 属性的默认值。
- `WhileEditing` 指示将在[`Entry`](xref:Xamarin.Forms.Entry)中显示 "清除" 按钮，同时包含焦点和文本。

下面的示例演示如何在 XAML 中设置属性：

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

等效 C# 代码如下：

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

以下屏幕截图显示了一个启用了 "清除" 按钮的[`Entry`](xref:Xamarin.Forms.Entry) ：

![在 iOS 和 Android 上使用 "清除" 按钮的项的屏幕截图](entry-images/entry-clear-button.png)

### <a name="customizing-the-keyboard"></a>自定义键盘

用户与[`Entry`](xref:Xamarin.Forms.Entry)交互时显示的键盘可以通过[`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)属性以编程方式设置为[`Keyboard`](xref:Xamarin.Forms.Keyboard)类中的以下属性之一：

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
<Entry Keyboard="Chat" />
```

等效 C# 代码如下：

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

等效 C# 代码如下：

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>自定义 Return 键

可以通过将[`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType)属性设置为[`ReturnType`](xref:Xamarin.Forms.ReturnType)枚举的值，自定义软键盘上的 return 键的外观，该键盘在[`Entry`](xref:Xamarin.Forms.Entry)具有焦点时显示：

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) –表示无需特定的返回密钥，将使用平台默认值。
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) –表示 "完成" 返回键。
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) –表示 "中转" 返回键。
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) –表示 "下一步" 返回键。
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) –表示 "搜索" 返回键。
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) –表示 "发送" 返回密钥。

下面的 XAML 示例演示如何设置返回的项：

```xaml
<Entry ReturnType="Send" />
```

等效 C# 代码如下：

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> 返回键的确切外观是依赖于平台。 在 iOS 上，返回的密钥是基于文本的按钮。 但是，在 Android 和通用 Windows 平台中，return 键是基于图标的按钮。

按下 return 键时，将激发[`Completed`](xref:Xamarin.Forms.Entry.Completed)事件，并执行[`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand)属性指定的任何 `ICommand`。 此外， [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter)属性指定的任何 `object` 都将作为参数传递给 `ICommand`。 有关命令的详细信息，请参阅[命令接口](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否启用拼写检查。 默认情况下，属性设置为 `true`。 当用户输入文本，指示拼写错误。

但对于某些文本输入方案，如输入用户名，拼写检查会提供消极的体验，并应通过将[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为 `false`来禁用：

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 如果[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为 `false`，并且未使用自定义键盘，则将禁用本机拼写检查器。 但是，如果已设置[`Keyboard`](xref:Xamarin.Forms.Keyboard)禁用拼写检查，如[`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat)，则将忽略 `IsSpellCheckEnabled` 属性。 因此，该属性不能用于对显式禁用该属性的 `Keyboard` 启用拼写检查。

### <a name="enabling-and-disabling-text-prediction"></a>启用和禁用文本预测

[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性控制是否启用文本预测和自动文本更正。 默认情况下，属性设置为 `true`。 当用户输入文本时，会显示 word 预测。

但是，对于某些文本输入方案，例如输入用户名，文本预测和自动文本更正将提供消极的体验，并应通过将[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性设置为 `false`来禁用：

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 如果将[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性设置为 `false`，并且未使用自定义键盘，则会禁用文本预测和自动文本更正。 但是，如果已设置了禁用文本预测的[`Keyboard`](xref:Xamarin.Forms.Keyboard) ，则将忽略 `IsTextPredictionEnabled` 属性。 因此，该属性不能用于对显式禁用它的 `Keyboard` 启用文本预测。

### <a name="colors"></a>颜色

若要使用的自定义背景和文本颜色通过以下可绑定属性，可以设置项：

- **TextColor** &ndash; 设置文本的颜色。
- **BackgroundColor** &ndash; 设置文本后面显示的颜色。

特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的默认值的文本和背景颜色，通常需要同时设置，如果你设置一个。

使用以下代码设置的项的文本颜色：

在 XAML:

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Entry TextColor 示例](entry-images/textcolor.png)

请注意，该占位符不受指定 `TextColor`的影响。

若要在 XAML 中设置的背景色：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Entry BackgroundColor 示例](entry-images/textbackgroundcolor.png)

请务必确保您选择的背景和文本颜色每个平台上可用，并且变得模糊的任何占位符文本。

## <a name="events-and-interactivity"></a>事件和交互性

项公开两个事件：

- 当项中的文本更改时， [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) &ndash; 引发。 更改之前和之后提供的文本。
- 当用户通过按键盘上的 return 键结束输入时， [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; 引发。

> [!NOTE]
> [`Entry`](xref:Xamarin.Forms.Entry)从中继承的[`VisualElement`](xref:Xamarin.Forms.VisualElement)类也具有[`Focused`](xref:Xamarin.Forms.VisualElement.Focused)和[`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件。

### <a name="completed"></a>已完成

`Completed` 事件用于响应与条目的交互的完成。 当用户通过按键盘上的 return 键（或按 UWP 上的 Tab 键）以字段结束输入时，将引发 `Completed`。 事件的处理程序是一个泛型事件处理程序，用于获取发送方和 `EventArgs`：

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

可以在 XAML 中订阅的已完成的事件：

```xaml
<Entry Completed="Entry_Completed" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

引发[`Completed`](xref:Xamarin.Forms.Entry.Completed)事件之后，将执行[`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand)属性指定的所有 `ICommand`，并通过将[`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter)属性指定的 `object` 传递到 `ICommand`。

### <a name="textchanged"></a>TextChanged

`TextChanged` 事件用于响应字段内容的更改。

当 `Entry` 的 `Text` 更改时，将引发 `TextChanged`。 事件的处理程序采用 `TextChangedEventArgs`的实例。 `TextChangedEventArgs` 通过 `OldTextValue` 和 `NewTextValue` 属性提供对 `Entry` `Text` 的新旧值的访问权限：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

可以在 XAML 中订阅 `TextChanged` 事件：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>相关链接

- [文本（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [输入 API](xref:Xamarin.Forms.Entry)
