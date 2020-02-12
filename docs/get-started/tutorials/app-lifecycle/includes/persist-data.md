---
ms.openlocfilehash: 5d9d5e4eb757d6afd1c13cb4851edd23feaa6e65
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135138"
---
[`Application`](xref:Xamarin.Forms.Application) 子类具有静态 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典，可用于跨生命周期状态更改存储数据。 该字典使用 `string` 密钥并存储 `object` 值。 字典会自动保存到设备中，并在应用程序重启时重新填充。

> [!IMPORTANT]
> [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典只能序列化存储基元类型。

在本练习中，你将修改应用程序以在后台运行时保留 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本，并在应用程序重启时将文本还原到 `Entry`。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“解决方案资源管理器”的 AppLifecycleTutorial 项目中，展开 App.xaml，然后双击 App.xaml.cs 将其打开     。 然后在“App.xaml.cs”中，删除所有模板代码并替换为以下代码  ：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    此代码定义 `DisplayText` 属性和 `displayText` 常数。 当应用程序在后台运行或终止时，`OnSleep` 方法覆盖会将 `DisplayText` 属性值添加到 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典，对应于 `displayText` 的密钥。 然后，当应用程序启动时，如果 `Properties` 字典包含 `displayText` 密钥，则该密钥的值将还原为 `DisplayText` 属性。

    > [!IMPORTANT]
    > 在访问密钥之前，请务必检查 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典是否存在密钥，以防止意外错误。

    没有必要从 `OnResume` 方法重载中的 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典还原数据。 这是因为当应用程序在后台运行时，该数据及其状态仍然在内存中。

1. 在“解决方案资源管理器”的“AppLifecycleTutorial”项目中，双击“MainPage.xaml”将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Entry`](xref:Xamarin.Forms.Entry) 组成。 [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性指定首次显示 `Entry` 时显示的占位符文本，并在 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件中注册名为 `OnEntryCompleted` 的事件处理程序。 此外，`Entry` 具有使用 `x:Name` 属性指定的名称。 该操作使代码隐藏文件能够使用分配给它的名称访问 `Entry` 对象。

1. 在“解决方案资源管理器”的“AppLifecycleTutorial”项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后，在“MainPage.xaml.cs”中，为 `OnAppearing` 方法添加覆盖，并为该类添加 `OnEntryCompleted` 事件处理程序  ：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    `OnAppearing` 方法检索 `App.DisplayText` 属性的值，并将其设置为 [`Entry`](xref:Xamarin.Forms.Entry) 的 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性值。

    > [!NOTE]
    > `OnAppearing` 方法覆盖的执行是在布局 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 后但在其变得可见之前进行的。 因此，这是设置 Xamarin.Forms 视图内容的好地方。

    当文本在 [`Entry`](xref:Xamarin.Forms.Entry) 中最终确定时，使用返回键执行 `OnEntryCompleted` 方法，`Entry` 文本存储在 `App.DisplayText` 属性中。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  。

    在 [`Entry`](xref:Xamarin.Forms.Entry) 中输入一些文本，并按返回键。 然后，通过点击“开始”按钮以调用 `OnSleep` 方法，在后台运行应用程序。

    最后，从 Visual Studio 中再次启动应用程序，将还原之前输入到 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本：

    [![iOS 和 Android 上文本属性跨生命周期状态更改持久保存的条目的屏幕截图](../images/persist-data.png "文本属性跨生命周期状态更改持久保存的条目")](../images/persist-data-large.png#lightbox "文本属性跨生命周期状态更改持久保存的条目")

    有关将数据持久保存到属性字典的详细信息，请参阅 [Xamarin.Forms 应用类](~/xamarin-forms/app-fundamentals/application-class.md)指南中的[属性字典](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“Solution Pad”中的 AppLifecycleTutorial 项目中，展开 App.xaml 并双击 App.xaml.cs 以将其打开     。 然后在“App.xaml.cs”中，删除所有模板代码并替换为以下代码  ：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    此代码定义 `DisplayText` 属性和 `displayText` 常数。 当应用程序在后台运行或终止时，`OnSleep` 方法覆盖会将 `DisplayText` 属性值添加到 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典，对应于 `displayText` 的密钥。 然后，当应用程序启动时，如果 `Properties` 字典包含 `displayText` 密钥，则该密钥的值将还原为 `DisplayText` 属性。

    > [!IMPORTANT]
    > 在访问密钥之前，请务必检查 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典是否存在密钥，以防止意外错误。

    没有必要从 `OnResume` 方法重载中的 [`Properties`](xref:Xamarin.Forms.Application.Properties) 字典还原数据。 这是因为当应用程序在后台运行时，该数据及其状态仍然在内存中。

1. 在“Solution Pad”的“AppLifecycleTutorial”项目中，双击“MainPage.xaml”将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Entry`](xref:Xamarin.Forms.Entry) 组成。 [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性指定首次显示 `Entry` 时显示的占位符文本，并在 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件中注册名为 `OnEntryCompleted` 的事件处理程序。 此外，`Entry` 具有使用 `x:Name` 属性指定的名称。 该操作使代码隐藏文件能够使用分配给它的名称访问 `Entry` 对象。

1. 在“Solution Pad”中的“AppLifecycleTutorial”项目中，展开“MainPage.xaml”并双击“MainPage.xaml.cs”以将其打开     。 然后，在“MainPage.xaml.cs”中，为 `OnAppearing` 方法添加覆盖，并为该类添加 `OnEntryCompleted` 事件处理程序  ：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    `OnAppearing` 方法检索 `App.DisplayText` 属性的值，并将其设置为 [`Entry`](xref:Xamarin.Forms.Entry) 的 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性值。

    > [!NOTE]
    > `OnAppearing` 方法覆盖的执行是在布局 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 后但在其变得可见之前进行的。 因此，这是设置 Xamarin.Forms 视图内容的好地方。

    当文本在 [`Entry`](xref:Xamarin.Forms.Entry) 中最终确定时，使用返回键执行 `OnEntryCompleted` 方法，`Entry` 文本存储在 `App.DisplayText` 属性中。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序  。

    在 [`Entry`](xref:Xamarin.Forms.Entry) 中输入一些文本，并按返回键。 然后，通过点击“开始”按钮以调用 `OnSleep` 方法，在后台运行应用程序。

    最后，从 Visual Studio for Mac 中再次启动应用程序，将还原之前输入到 [`Entry`](xref:Xamarin.Forms.Entry) 中的文本：

    [![iOS 和 Android 上文本属性跨生命周期状态更改持久保存的条目的屏幕截图](../images/persist-data.png "文本属性跨生命周期状态更改持久保存的条目")](../images/persist-data-large.png#lightbox "文本属性跨生命周期状态更改持久保存的条目")

    有关将数据持久保存到属性字典的详细信息，请参阅 [Xamarin.Forms 应用类](~/xamarin-forms/app-fundamentals/application-class.md)指南中的[属性字典](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary)。
