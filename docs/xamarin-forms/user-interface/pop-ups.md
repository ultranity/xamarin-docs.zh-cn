---
title: 显示弹出窗口
description: Xamarin 提供三个类似于弹出式窗口的用户界面元素-警报、操作表和提示。 本文演示如何使用警报、操作表和提示 Api 来显示询问用户简单问题的对话框、指导用户完成任务以及显示提示。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/17/2020
ms.openlocfilehash: c71153cdaa94a7983b89968abc828011a648f2b1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636094"
---
# <a name="display-pop-ups"></a>显示弹出窗口

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

显示警报，要求用户进行选择或显示提示是常见的 UI 任务。 Xamarin 在[`Page`](xref:Xamarin.Forms.Page)类上有三种方法，用于通过弹出窗口与用户进行交互： [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)、 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)和 `DisplayPromptAsync`。 在每个平台上使用相应的本机控件呈现。

## <a name="display-an-alert"></a>显示警报

所有 Xamarin.Forms 支持的平台都会弹出一个模式，提醒用户或向用户提出简单的问题。 要在 Xamarin.Forms 中显示这些警报，请在任何 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 上使用 [`Page`](xref:Xamarin.Forms.Page) 方法。 以下代码行向用户显示一个简单的消息：

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Alert Dialog with One Button")

本例不收集用户的信息。 警报以模式显示，一旦用户被解雇，就会继续与应用程序进行交互。

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法还可以通过显示两个按钮并返回 `boolean` 来捕获用户的响应。 要从警报中获得响应，请为两个按钮和 `await` 方法提供文本。 在用户选择其中一个选项后，答案将返回到你的代码。 注意下面示例代码中的 `async` 和 `await` 关键字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "包含两个按钮的警报对话框")](pop-ups-images/alert2.png#lightbox "包含两个按钮的警报对话框")

## <a name="guide-users-through-tasks"></a>指导用户完成任务

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) 是 iOS 中的常见 UI 元素。 Xamarin.Forms[`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法可以在跨平台应用程序中包含此控件，并在 Android 和 UWP 中呈现本机备选方案。

若要显示操作表，请在任意[`Page`](xref:Xamarin.Forms.Page)中 `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) ，并以字符串的形式传递消息和按钮标签。 该方法返回供用户单击的按钮的字符串标签。 下面是简单示例：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet Dialog")

`destroy` 按钮的呈现方式与其他按钮不同，可以保留 `null` 或指定为第三个字符串参数。 下面的示例使用 `destroy` 按钮：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "带有销毁按钮的操作表对话框")](pop-ups-images/action2.png#lightbox "带有销毁按钮的操作表对话框")

## <a name="display-a-prompt"></a>显示提示

若要显示提示，请调用任何[`Page`](xref:Xamarin.Forms.Page)中的 `DisplayPromptAsync`，并将标题和消息作为 `string` 参数传递：

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

提示符以模式方式显示：

[![IOS 和 Android 上的模式提示屏幕截图](pop-ups-images/simple-prompt.png "模式提示")](pop-ups-images/simple-prompt-large.png#lightbox "模式提示")

如果点击 "确定" 按钮，则输入的响应将作为 `string`返回。 如果点击 "取消" 按钮，则返回 `null`。

`DisplayPromptAsync` 方法的完整参数列表为：

- `string`类型的 `title`，则是要在提示中显示的标题。
- `message`，类型 `string`，是要在提示符处显示的消息。
- `string`类型的 `accept`是 "接受" 按钮的文本。 这是一个可选参数，其默认值为 "正常"。
- `string`类型的 `cancel`是 "取消" 按钮的文本。 这是一个可选参数，其默认值为 "取消"。
- `string`类型的 `placeholder`是要在提示符处显示的占位符文本。 这是一个可选参数，其默认值为 `null`。
- `int`类型的 `maxLength`是用户响应的最大长度。 这是一个可选参数，其默认值为-1。
- 类型 `Keyboard``keyboard`，是用于用户响应的键盘类型。 这是一个可选参数，其默认值为 `Keyboard.Default`。
- `string`类型的 `initialValue`是将显示的预定义的响应，可对其进行编辑。 这是一个可选参数，其默认值为空 `string`。

下面的示例演示如何设置一些可选参数：

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

此代码显示预定义的10个响应，限制可输入的字符数2，并显示用户输入的数字键盘：

[![IOS 和 Android 上的模式提示屏幕截图](pop-ups-images/keyboard-prompt.png "模式提示")](pop-ups-images/keyboard-prompt-large.png#lightbox "模式提示")

> [!NOTE]
> `DisplayPromptAsync` 方法目前仅在 iOS 和 Android 上实现。

## <a name="related-links"></a>相关链接

- [PopupsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
