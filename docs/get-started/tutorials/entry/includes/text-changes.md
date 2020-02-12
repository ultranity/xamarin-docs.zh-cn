---
ms.openlocfilehash: deb3516cc134a8b2eecba8460931003de8bb312f
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135133"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“MainPage.xaml”中，修改 [`Entry`](xref:Xamarin.Forms.Entry) 声明，以便为 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件设置处理程序  ：

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    此代码将 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件设置为名为 `OnEntryTextChanged` 的事件处理程序，将 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件设置为名为 `OnEntryCompleted` 的事件处理程序。 这两个事件处理程序都将在下一步中进行创建。

1. 在“解决方案资源管理器”的 EntryTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后在 MainPage.xaml.cs 中，将 `OnEntryTextChanged` 和 `OnEntryCompleted` 事件处理程序添加到类  ：

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    当 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本更改时，将执行 `OnEntryTextChanged` 方法。 `sender` 参数是负责触发 `TextChanged` 事件的 `Entry` 对象，还可用于访问 `Entry` 对象。 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 参数提供了文本更改之前和之后的旧文本值和新文本值。

    使用返回键完成 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本时，将执行 `OnEntryCompleted` 方法。 `sender` 参数是负责触发 `TextChanged` 事件的 `Entry` 对象，还可用于访问 `Entry` 对象。

    > [!IMPORTANT]
    > 向 [`Entry`](xref:Xamarin.Forms.Entry) 输入的任何文本都将存储在 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性中。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  ：

    [![iOS 和 Android 上包含文本的条目的屏幕截图](../images/text-changes.png "包含文本的条目")](../images/text-changes-large.png#lightbox "包含文本的条目")

    在两个事件处理程序中设置断点，向 [`Entry`](xref:Xamarin.Forms.Entry) 输入文本并观察 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件触发。

    有关 [`Entry`](xref:Xamarin.Forms.Entry) 事件的详细信息，请参阅 [Xamarin.Forms 条目](~/xamarin-forms/user-interface/text/entry.md)指南中的[事件和交互](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“MainPage.xaml”中，修改 [`Entry`](xref:Xamarin.Forms.Entry) 声明，以便为 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件设置处理程序  ：

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    此代码将 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件设置为名为 `OnEntryTextChanged` 的事件处理程序，将 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件设置为名为 `OnEntryCompleted` 的事件处理程序。 这两个事件处理程序都将在下一步中进行创建。

1. 在 Solution Pad 的 EntryTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后在 MainPage.xaml.cs 中，将 `OnEntryTextChanged` 和 `OnEntryCompleted` 事件处理程序添加到类  ：

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    当 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本更改时，将执行 `OnEntryTextChanged` 方法。 `sender` 参数是负责触发 `TextChanged` 事件的 `Entry` 对象，还可用于访问 `Entry` 对象。 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 参数提供了文本更改之前和之后的旧文本值和新文本值。

    使用返回键完成 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本时，将执行 `OnEntryCompleted` 方法。 `sender` 参数是负责触发 `TextChanged` 事件的 `Entry` 对象，还可用于访问 `Entry` 对象。

    > [!IMPORTANT]
    > 向 [`Entry`](xref:Xamarin.Forms.Entry) 输入的任何文本都将存储在 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性中。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序  ：

    [![iOS 和 Android 上包含文本的条目的屏幕截图](../images/text-changes.png "包含文本的条目")](../images/text-changes-large.png#lightbox "包含文本的条目")

    在两个事件处理程序中设置断点，向 [`Entry`](xref:Xamarin.Forms.Entry) 输入文本并观察 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件触发。

    有关 [`Entry`](xref:Xamarin.Forms.Entry) 事件的详细信息，请参阅 [Xamarin.Forms 条目](~/xamarin-forms/user-interface/text/entry.md)指南中的[事件和交互](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity)。
