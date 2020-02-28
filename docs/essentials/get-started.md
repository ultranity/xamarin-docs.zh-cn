---
title: Xamarin.Essentials 入门
description: Xamarin.Essentials 提供了适用于任何 iOS、Android 或 UWP 应用程序的单一跨平台 API，不管如何创建用户界面，都可以通过共享代码进行访问。
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 07/10/2019
ms.openlocfilehash: daab2b3a18eeea2f50c9518144b3396fbba90928
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545130"
---
# <a name="get-started-with-xamarinessentials"></a>Xamarin.Essentials 入门

Xamarin.Essentials 提供了适用于任何 iOS、Android 或 UWP 应用程序的单一跨平台 API，不管如何创建用户界面，都可以通过共享代码进行访问。 有关支持的操作系统的详细信息，请参阅[平台和功能支持指南](platform-feature-support.md)。

## <a name="installation"></a>安装

Xamarin.Essentials 可用作 NuGet 包并包含在 Visual Studio 的每个新项目中。 通过执行以下步骤，还可以使用 Visual Studio 将其添加到任何现有项目。

1. 使用 [Visual Studio tools for Xamarin](~/get-started/installation/index.md) 下载并安装 [Visual Studio](https://visualstudio.microsoft.com/)。

2. 使用  Visual Studio C#（Android、iPhone 和 iPad 或跨平台）下的空白应用模板打开现有项目，或创建新项目。

    > [!IMPORTANT]
    > 若要添加到 UWP 项目，请务必在项目属性中设置内部版本 16299 或更高版本。

3. 将 [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) NuGet 包添加到每个项目：

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    在“解决方案资源管理器”面板中，右键单击解决方案名称，然后选择“管理 NuGet 包”  。 搜索  Xamarin.Essentials 并将包安装到  所有项目，包括 Android、iOS、UWP 和 .NET Standard 库。

    # <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

    在“解决方案资源管理器”面板中，右键单击项目名称，然后选择“添加”>“添加 NuGet 包...”  。搜索  Xamarin.Essentials 并将包安装到  所有项目，包括 Android、iOS 和 .NET Standard 库。

    -----

4. 在任何 C# 类中添加对 Xamarin.Essentials 的引用以引用 API。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials 需要特定于平台的设置：

    # <a name="android"></a>[Android](#tab/android)

    Xamarin.Essentials 支持最低 Android 版本 4.4（对应于 API 级别 19），但用于编译的目标 Android 版本必须为 9.0（对应于 API 级别 28）。 （在 Visual Studio 中，已在“Android 清单”选项卡中的 Android 项目的“项目属性”对话框中设置这两个版本。在 Visual Studio for Mac 中，已在“Android 应用程序”选项卡中的 Android 项目的“项目选项”对话框中设置这两个版本。）

    Xamarin.Essentials 安装所需的 Xamarin.Android.Support 库版本 28.0.0.3。 还应使用 NuGet 包管理器将应用所需的其他任何 Xamarin.Android.Support 库更新到版本 28.0.0.3。 应用使用的所有 Xamarin.Android.Support 库都应相同，且版本不得低于 28.0.0.3。 如果在解决方案中添加 Xamarin.Essentials NuGet 或更新 Nuget 时遇到问题，请参阅[疑难解答页面](troubleshooting.md)。

    在 Android 项目的 `MainLauncher` 或任何启动的 `Activity` 中，必须在 `OnCreate` 方法中初始化 Xamarin.Essentials：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    若要处理 Android 上的运行时权限，Xamarin.Essentials 必须接收任何 `OnRequestPermissionsResult`。 将以下代码添加到所有 `Activity` 类：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="ios"></a>[iOS](#tab/ios)

    无需其他设置。

    # <a name="uwp"></a>[UWP](#tab/uwp)

    无需其他设置。

    -----

6. 按照 [Xamarin.Essentials 指南](index.md)为每个功能复制并粘贴代码片段。

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials - 适用于移动应用的跨平台 API（视频）

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>其他资源

建议刚开始接触 Xamarin 的开发人员访问 [Xamarin 开发入门](~/cross-platform/getting-started/index.md)。

访问 [Xamarin.Essentials GitHub 存储库](https://github.com/xamarin/Essentials)查看当前源代码，接下来，运行示例，并克隆存储库。 欢迎为社区做贡献！

浏览 [API 文档](xref:Xamarin.Essentials)了解每个 Xamarin.Essentials 功能。
