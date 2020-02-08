---
title: 设置用于 Xamarin.Android 的 Android SDK
description: Visual Studio 包含 Android SDK 管理器，用于下载 Android SDK 工具、平台以及开发 Xamarin.Android 应用所需的其他组件。
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 6e80daf2be1b672dc7d0de3e303dfb9261c10472
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940905"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>设置用于 Xamarin.Android 的 Android SDK

_Visual Studio 包含 Android SDK 管理器，用于下载 Android SDK 工具、平台以及开发 Xamarin.Android 应用所需的其他组件。_

## <a name="overview"></a>概述

本指南介绍如何在 Visual Studio 和 Visual Studio for Mac 中使用 Xamarin Android SDK 管理器。

> [!NOTE]
> 本指南适用于 Visual Studio 2019、Visual Studio 2017 和 Visual Studio for Mac。  

Xamarin Android SDK 管理器（作为 .NET 移动开发的一部分安装）可帮助你下载开发 Xamarin.Android 应用所需的最新 Android 组件。 它取代了已被弃用的 Google 的独立 SDK 管理器。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="requirements"></a>要求

若要使用 Xamarin Android SDK 管理器，需要以下工具：

- Visual Studio 2019 Community、Professional 或 Enterprise。

- 或者 Visual Studio 2017（Community、Professional 或 Enterprise 版本）。 需要 Visual Studio 2017 版本 15.7 或更高版本。

- Visual Studio Tools for Xamarin 版本 4.10.0 或更高版本（作为使用 .NET 的移动开发工作负载的一部分安装）。 

Xamarin Android SDK 管理器还需要 Java 开发工具包（此工具包自动安装在 Xamarin.Android 中）。 有多种 JDK 可选方案供选择：

- Xamarin.Android 默认使用 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，这是在为 API 级别 24 或更高级别进行开发时所必需的（JDK 8 还支持低于 24 的 API 级别）。

- 如果专门为 API 级别 23 或更低级别进行开发，可以继续使用 [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

- 如果使用 Visual Studio 15.8 Preview 5 或更高版本，可尝试使用 [Microsoft Mobile OpenJDK 分发](openjdk.md)而不使用 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

## <a name="sdk-manager"></a>SDK 管理器 

若要在 Visual Studio 中启动 SDK 管理器，请单击“工具”>“Android”>“Android SDK 管理器”：

[![“Android SDK 管理器”菜单项的位置](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

Android SDK 管理器会在“Android SDK 和工具”屏幕中打开。 此屏幕上有两个选项卡 &ndash;“平台”和“工具”：

[![“平台”选项卡中打开的 Android SDK 管理器的屏幕截图](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

下面的部分将对“Android SDK 和工具”屏幕进行更详细的介绍。

### <a name="android-sdk-location"></a>Android SDK 位置

Android SDK 位置是在“Android SDK 和工具”屏幕的顶部进行配置的，如以上屏幕截图中所示。 必须正确配置此位置，这样“平台”和“工具”选项卡才能正常工作。 出于下面一个或多个原因，可能需要设置 Android SDK 的位置：

1. Android SDK 管理器无法定位 Android SDK。 

2. 你已在备用（非默认）位置安装了 Android SDK。 

若要设置 Android SDK 的位置，请单击“Android SDK 位置”最右侧的省略号 (&hellip;) 按钮。 这将打开“浏览文件夹”对话框，用于导航到 Android SDK 的位置。 在下面的屏幕截图中，已选中“Program Files (x86)\\Android”下面的 Android SDK：

![定位 android sdk 的 Windows“浏览文件夹”的屏幕截图](android-sdk-images/win/05-browse-for-folder.png)

单击“确定”后，SDK 管理器将管理安装在所选位置的 Android SDK。

### <a name="tools-tab"></a>“工具”选项卡

“工具”选项卡显示“工具”和“附加程序”的列表。 使用此选项卡以安装 Android SDK 工具、平台工具和生成工具。
此外，还可以安装 Android Emulator、低级别调试器 (LLDB)、NDK、HAXM 加速和 Google Play 库。

例如，若要下载 Google Android Emulator 包，请单击“Android Emulator”旁的复选标记，然后单击“应用更改”按钮：

[![从“工具”选项卡中安装 Android Emulator](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

可能显示包含“以下包需要你在安装前接受其许可条款:”消息的对话框

![“许可证接受”屏幕](android-sdk-images/win/07-license-acceptance.png)

如果接受这些条款和条件，请单击“接受”。 在窗口底部，有一个进度栏会指示下载和安装进度。 安装完成后，“工具”选项卡将显示已安装所选的工具和附加程序。

### <a name="platforms-tab"></a>“平台”选项卡

“平台”选项卡显示平台 SDK 版本以及适用于每个平台的其他资源（例如系统映像）的列表:

[![“平台”窗格的屏幕截图](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

此屏幕列出了 Android 版本（例如“Android 8.0”）、代码名称（“Oreo”）、API 级别（例如“26”）以及平台对应组件的大小（例如“1 GB”）。 使用“平台”选项卡安装要面向的 Android API 级别的组件。 （有关 Android 版本和 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)）。

安装某个平台的所有组件后，该平台名称旁边将显示一个复选标记。 如果某个平台的组件并非已全部安装，该平台的此框则会被填充。 可以通过单击某个平台左侧的“+”框展开此平台以查看其组件（以及已安装的组件）。
单击“-”取消展开某个平台的组件列表。

若要将另一个平台添加到 SDK，请单击此平台旁边的框 - 直到显示复选标记 - 以安装其所有组件，然后单击“应用更改”：

[![将 Android 7.1 Nougat 组件添加到 Android SDK 的示例](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

若要仅安装特定组件，请单击一次此平台旁边的框。 然后可以选择所需的任何单个组件：

[![添加某些 Android 7.1 组件的示例](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

请注意，要安装的组件数量显示在“应用更改”按钮旁边。 单击“应用更改”按钮后，将看到上示“许可证接受”屏幕。
如果接受这些条款和条件，请单击“接受”。 如果有多个组件要安装，则可能会多次看到此对话框。 在窗口底部，有一个进度栏会指示下载和安装进度。 下载和安装过程完成后（可能需要好几分钟的时间，具体取决于需要下载的组件数量），已添加的组件会被复选标记标记出来并被列为“已安装”。

### <a name="repository-selection"></a>存储库选择

默认情况下，Android SDK 管理器从由 Microsoft 托管的存储库下载平台组件和工具。 如果需要访问实验性 Alpha/Beta 平台和 Microsoft 存储库中尚不可用的工具，可以将 SDK 管理器切换为使用 Google 的存储库。 若要进行这种切换，请单击右下角的齿轮图标，然后选择“存储库”>“Google (不受支持)”：

[![选择 Google 的存储库](android-sdk-images/win/11-google-repo-w157-sml.png)](android-sdk-images/win/11-google-repo-w157.png#lightbox)

选择 Google 存储库后，以前不可用的“平台”选项卡中可能显示其他包。 （在上面的屏幕截图中，切换到 Google 存储库后添加了“Android SDK 平台 28”。）请注意，使用 Google 存储库不受支持，因此不建议将其用于日常开发。

若要切换回平台和工具支持的存储库，请单击“Microsoft (推荐)”。 这会将包和工具的列表还原到默认选择。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="requirements"></a>要求

若要使用 Xamarin Android SDK 管理器，需要以下工具：

- Visual Studio for Mac 7.5（或更高版本）。

Xamarin Android SDK 管理器还需要 Java 开发工具包（此工具包自动安装在 Xamarin.Android 中）。 有多种 JDK 可选方案供选择：

- Xamarin.Android 默认使用 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，这是在为 API 级别 24 或更高级别进行开发时所必需的（JDK 8 还支持低于 24 的 API 级别）。

- 如果专门为 API 级别 23 或更低级别进行开发，可以继续使用 [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

- 如果使用 Visual Studio for Mac 7.7 或更高版本，可尝试使用 [Microsoft Mobile OpenJDK 分发](openjdk.md)而不使用 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

## <a name="sdk-manager"></a>SDK 管理器 

若要在 Visual Studio for Mac 中启动 SDK 管理器，请单击“工具”>“SDK 管理器”：

[![“Android SDK 管理器”菜单项的位置](android-sdk-images/mac/01-sdk-manager-menu-item-m75-sml.png)](android-sdk-images/mac/01-sdk-manager-menu-item-m75.png#lightbox)

“Android SDK 管理器”会在“首选项窗口”中打开，此窗口中包含“平台”、“工具”和“位置”这三个选项卡：

[![“平台”选项卡中打开的 Android SDK 管理器的屏幕截图](android-sdk-images/mac/02-sdk-manager-platforms-m75-sml.png)](android-sdk-images/mac/02-sdk-manager-platforms-m75.png#lightbox)

下面的部分将对 Android SDK 管理器的各个选项卡进行介绍。

### <a name="locations-tab"></a>“位置”选项卡

“位置”选项卡具有用于配置 Android SDK、Android NDK 和 Java SDK (JDK) 位置的三个设置。 必须正确配置这些位置，这样“平台”和“工具”选项卡才能正常工作。

SDK 管理器启动后，它会自动确定每个已安装包的路径，并通过在此路径旁放置一个绿色的复选标记图标以指示“已找到”路径：

[![“位置”选项卡的屏幕截图](android-sdk-images/mac/03-locations-tab-m75-sml.png)](android-sdk-images/mac/03-locations-tab-m75.png#lightbox)

单击“重置为默认值”按钮，使 SDK 管理器在 SDK、NDK 和 JDK 的默认位置查找它们。 

通常情况下，使用“位置”选项卡来修改 Android SDK 和/或 Java JDK 的位置。 不需要安装 NDK 来开发 Xamarin.Android 应用 &ndash; 只有当需要使用 C 和 C++ 等本机代码的语言开发应用的一部分时才使用 NDK。

### <a name="tools-tab"></a>“工具”选项卡

“工具”选项卡显示“工具”和“附加程序”的列表。 使用此选项卡以安装 Android SDK 工具、平台工具和生成工具。
此外，还可以安装 Android Emulator、低级别调试器 (LLDB)、NDK、HAXM 加速和 Google Play 库。

例如，若要下载 Google Android Emulator 包，请单击“Android Emulator”旁的复选标记，然后单击“应用更改”按钮：

[![从“工具”选项卡中安装 Android Emulator](android-sdk-images/mac/04-tools-tab-m75-sml.png)](android-sdk-images/mac/04-tools-tab-m75.png#lightbox)

可能显示包含“以下包需要你在安装前接受其许可条款:”消息的对话框

[![“许可证接受”屏幕](android-sdk-images/mac/05-license-acceptance-m75-sml.png)](android-sdk-images/mac/05-license-acceptance-m75.png#lightbox)

如果接受这些条款和条件，请单击“接受”。 在窗口底部，有一个进度栏会指示下载和安装进度。 安装完成后，“工具”选项卡将显示已安装所选的工具和附加程序。

### <a name="platforms-tab"></a>“平台”选项卡

“平台”选项卡显示平台 SDK 版本以及适用于每个平台的其他资源（例如系统映像）的列表:

[![“平台”窗格的屏幕截图](android-sdk-images/mac/06-platforms-tab-m75-sml.png)](android-sdk-images/mac/06-platforms-tab-m75.png#lightbox)

此屏幕列出了 Android 版本（例如“Android 8.1”）、代码名称（“Oreo”）、API 级别（例如“27”）以及平台对应组件的大小（例如“1 GB”）。 使用“平台”选项卡安装要面向的 Android API 级别的组件。 （有关 Android 版本和 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)）。

安装某个平台的所有组件后，该平台名称旁边将显示一个复选标记。 如果某个平台的组件并非已全部安装，该平台的此框则会被填充。 可以通过单击某个平台左侧的“箭头”展开此平台以查看其组件（以及已安装的组件）。
单击“向下箭头”以取消展开某个平台的组件列表。

若要将另一个平台添加到 SDK，请单击此平台旁边的框 - 直到显示复选标记 - 以安装其所有组件，然后单击“应用更改”：

[![添加某个平台的所有组件的示例](android-sdk-images/mac/07-install-all-m75-sml.png)](android-sdk-images/mac/07-install-all-m75.png#lightbox)

若要仅安装某些组件，请单击一次此平台旁边的框。 然后可以选择所需的任何单个组件：

[![添加某些组件的示例](android-sdk-images/mac/08-individual-components-m75-sml.png)](android-sdk-images/mac/08-individual-components-m75.png#lightbox)

请注意，要安装的组件数量显示在“应用更改”按钮旁边。 单击“应用更改”按钮后，将看到上示“许可证接受”屏幕。
如果接受这些条款和条件，请单击“接受”。 如果有多个组件要安装，则可能会多次看到此对话框。 在窗口底部，有一个进度栏会指示下载和安装进度。 下载和安装过程完成后（可能需要好几分钟的时间，具体取决于需要下载的组件数量），已添加的组件会被复选标记标记出来并被列为“已安装”。

### <a name="repository-selection"></a>存储库选择

默认情况下，Android SDK 管理器从由 Microsoft 托管的存储库下载平台组件和工具。 如果需要访问实验性 Alpha/Beta 平台和 Microsoft 存储库中尚不可用的工具，可以将 SDK 管理器切换为使用 Google 的存储库。 若要进行这种切换，请单击右下角的齿轮图标，然后选择“存储库”>“Google (不受支持)”：

[![选择 Google 的存储库](android-sdk-images/mac/09-google-repo-m75-sml.png)](android-sdk-images/mac/09-google-repo-m75.png#lightbox)

选择 Google 存储库后，以前不可用的“平台”选项卡中可能显示其他包。 （在上面的屏幕截图中，切换到 Google 存储库后添加了“Android SDK 平台 28”。）请注意，使用 Google 存储库不受支持，因此不建议将其用于日常开发。

若要切换回平台和工具支持的存储库，请单击“Microsoft (推荐)”。 这会将包和工具的列表还原到默认选择。

-----

## <a name="summary"></a>总结

本指南说明了如何在 Visual Studio 和 Visual Studio for Mac 中安装和使用 Xamarin Android SDK 管理器工具。

## <a name="related-links"></a>相关链接

- [了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)
- [对 Android SDK 工具的更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
