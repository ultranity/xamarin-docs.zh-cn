---
ms.openlocfilehash: fe7e11f37f303c2ec1f5bc58e98953449694191c
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135122"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要完成本教程，应使用 Visual Studio 2019（最新版本），且安装了“使用 .NET 的移动开发”工作负载  。 此外，还需要一个匹配的 Mac，用于在 iOS 上生成教程应用程序。 有关安装 Xamarin 平台的信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。 有关将 Visual Studio 2019 连接到 Mac 生成主机的信息，请参阅[通过“与 Mac 配对”进行 Xamarin.iOS 开发](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 启动 Visual Studio，新建空白的名为 EditorTutorial 的 Xamarin.Forms 应用  。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 EditorTutorial  。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”的“EditorTutorial”项目中，双击“MainPage.xaml”将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Editor`](xref:Xamarin.Forms.Editor) 组成。 [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性指定第一次显示 `Editor` 时显示的占位符文本。 此外，[`HeightRequest`](xref:Xamarin.Forms.VisualElement) 属性指定设备独立单元中 `Editor` 的高度。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  ：

    [![iOS 和 Android 上的编辑器屏幕截图](../images/create-editor.png "包含占位符文本的编辑器")](../images/create-editor-large.png#lightbox "包含占位符文本的编辑器")

    > [!NOTE]
    > 虽然 Android 指示 [`Editor`](xref:Xamarin.Forms.Editor) 的高度，但 iOS 并不指示。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教程，应使用 Visual Studio for Mac（最新版），且安装了 iOS 和 Android 平台支持。 此外，还需要 Xcode（最新版）。 有关安装 Xamarin 平台的详细信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。

1. 启动 Visual Studio for Mac，新建空白的名为 EditorTutorial 的 Xamarin.Forms 应用  。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 EditorTutorial  。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“Solution Pad”的“EditorTutorial”项目中，双击“MainPage.xaml”将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Editor`](xref:Xamarin.Forms.Editor) 组成。 [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性指定第一次显示 `Editor` 时显示的占位符文本。 此外，[`HeightRequest`](xref:Xamarin.Forms.VisualElement) 属性指定设备独立单元中 `Editor` 的高度。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序  ：

    [![iOS 和 Android 上的编辑器屏幕截图](../images/create-editor.png "包含占位符文本的编辑器")](../images/create-editor-large.png#lightbox "包含占位符文本的编辑器")

    > [!NOTE]
    > 虽然 Android 指示 [`Editor`](xref:Xamarin.Forms.Editor) 的高度，但 iOS 并不指示。
