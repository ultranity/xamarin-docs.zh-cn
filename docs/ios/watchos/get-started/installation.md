---
title: 安装和在 Xamarin 中使用 watchOS
description: 本文档介绍如何安装和使用 Xamarin 使用 watchOS。 此外，介绍了安装，watchOS 项目结构，如何使用 iOS 设计器、 Xcode 集成，并提供了故障排除提示。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: f986099011dbccb0eb43c62d253ee497d46ca08e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915537"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>安装和在 Xamarin 中使用 watchOS

watchOS 4 需要 macOS Sierra (10.12) 使用 Xcode 9。

在 watchOS 1 中最初所需使用 Xcode 7 的 OS X Yosemite (10.10)。

> [!WARNING]
> [2018 年4月1日之后，将不会接受 watchOS 1 更新](https://developer.apple.com/news/?id=11162017a)。 将来的更新必须使用 watchOS 2 SDK 或更高版本;在 watchos 生成建议 4 SDK。

## <a name="project-structure"></a>项目结构

Watch 应用包含三个项目：

- **Xamarin IOS iphone 应用项目**-这是一个普通的 iPhone 项目，它可以是任何 Xamarin iOS 模板。 监视应用和其扩展将被捆绑在该主项目内。

- **监视扩展项目**-此项包含 Watch 应用程序的代码（如控制器类）。

- **监视应用项目**-此项包含用户界面情节提要文件，其中包含用于监视应用的所有 UI 资源。

在 Xamarin Studio 中，[监视工具包目录示例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)解决方案如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/catalog-solution.png "The solution in Visual Studio")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "The solution in Visual Studio")

-----

下载并运行[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)示例以开始工作。
可以在 "[控件](~/ios/watchos/user-interface/index.md)" 页上找到该示例中的屏幕。

## <a name="creating-a-new-project"></a>创建新项目

您不能创建一个新"监视解决方案"...而是可以将监视应用程序添加到现有的 iOS 应用程序。 请按照以下步骤创建的监视应用操作：

1. 如果没有现有项目，请先选择 "**文件" > "新建解决方案**" 并创建一个 iOS 应用（例如，**单个视图应用**）：

    [![](installation-images/cycle8-2-sml.png "Choose File > New Solution and create an iOS app")](installation-images/cycle8-2.png#lightbox)

2. 创建 iOS 应用（或计划使用现有的 iOS 应用）后，右键单击该解决方案，然后选择 "添加" > "**添加新项目 ...** "在 "**新建项目**" 窗口中，选择 " **watchOS > 应用 > WatchKit 应用**：

    [![](installation-images/cycle8-6-sml.png "Select watchOS > App > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. 在下一屏幕中，您选择的 iOS 应用程序项目应包括 watch 应用：

    [![](installation-images/cycle8-7-sml.png "Choose which iOS app project should include the watch app")](installation-images/cycle8-7.png#lightbox)

4. 最后，选择要保存项目的位置 （和根据需要启用源代码管理）：

    [![](installation-images/cycle8-8-sml.png "Choose the location to save the project")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio for Mac 自动为你配置[项目引用和**info.plist**设置](~/ios/watchos/get-started/project-references.md)。

## <a name="creating-the-watch-user-interface"></a>创建监视用户界面

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

双击 "监视" 应用的**界面**，使用 IOS 设计器进行编辑。 可以将界面控制器和 UI 控件从**工具箱**拖动到情节提要，并使用 "**属性**" 板配置它们：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "The storyboard in the Designer")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "The storyboard in the Designer")](installation-images/iosdesigner-vs.png#lightbox)

-----

你应通过选择一个类，然后在**Properties** pad 中输入名称，为每个新的接口控制器提供一个**类**（这C#将自动创建所需的代码隐藏文件）：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Give each new interface controller a Class")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Give each new interface controller a Class")

-----

通过按**Ctrl 并**将按钮、表或接口控制器拖动到另一个接口控制器来创建 segue。

### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

右键单击 Xcode 文件并选择 "打开方式"，然后选择 "**打开方式" > Xcode Interface Builder**，可以继续使用生成用户界面：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Visual Studio 用户还可以使用 Xcode 切换要直接使用 Mac 生成主机生成用户界面。
在 Visual Studio for Mac 中打开解决方案，然后右键单击 "Xcode" 文件，然后选择 "**打开方式" > "Interface Builder**：

-----

![](installation-images/openwith-xcode.png "Open the Interface.storyboard in Xcode Interface Builder")

如果使用的是 Xcode，则应遵循适用于普通[iOS 应用情节提要](~/ios/user-interface/storyboards/index.md)的监视应用的相同步骤（例如通过按**Ctrl 键并拖**入 **.h**头文件来创建输出口和操作）。

在 Xcode 中保存情节提要后 Interface Builder 会自动将所创建的插座和操作添加到 " C#监视扩展" 项目中的**designer.cs**文件。

### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中添加其他屏幕

当你使用 Interface Builder Xcode 向情节提要添加其他屏幕（默认情况下模板中包含的内容）时，你必须为每个新的接口控制器**手动添加C#代码文件**。

请参阅[有关如何向情节提要添加新的界面控制器的高级说明](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 设计器自动执行此操作，无需手动步骤。*

## <a name="building"></a>生成

包含的监视应用的项目，生成与其他 iOS 项目一样。 生成过程会导致包含监视扩展 (.appex)，它包含无代码的监视应用程序 (.app) 的 iPhone 应用程序 (.app)。

## <a name="launching"></a>启动

您可以启动监视应用程序在模拟器中使用 Visual Studio for Mac 或 Visual Studio （Mac 生成主机上启动）。

有两种模式启动的 WatchKit 应用：

- 正常的应用程序模式 （默认值），并
- [通知](~/ios/watchos/platform/notifications.md)（需要 JSON 格式的测试通知有效负载）。

### <a name="xcode-8-support"></a>Xcode 8 支持

安装 Xcode 8 （或更高版本）后，Apple Watch 模拟器独立于 iOS 模拟器（不同于[Xcode 6](#xcode6)，它们显示为*外部显示*）。
选择 "监视应用项目" 并将其设为 "启动项目" 时，模拟器列表将显示要从中进行选择的*IOS 模拟器*（如下所示）。

[![](installation-images/xs-xcode8-watchos3-sml.png "Selecting the Simulator type")](installation-images/xs-xcode8-watchos3.png#lightbox)

开始调试时，应启动*两个*模拟器-iOS 模拟器*和*Apple Watch 模拟器。 使用**Command + Shift + H**导航到 "监视" 菜单和 "时钟面";并使用**硬件**菜单设置**Force Touch 压力**。 滚动鼠标的触控板上将模拟使用数字 Crown。

#### <a name="troubleshooting"></a>故障排除

如果尝试启动到不具有配对监视的模拟器，则会在**应用程序输出**中显示以下错误：

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

如果默认值不起作用，请参阅[Apple 的论坛](https://forums.developer.apple.com/thread/7783)了解有关配置模拟器的说明。

<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

在运行或调试应用之前，必须使*监视扩展项目*成为**启动项目**。 无法"启动"watch 应用本身，并且如果您选择的 iOS 应用，它将启动 iOS 模拟器中作为普通。

默认情况下，"监视" 应用从 Visual Studio for Mac 的 "**运行**" 或 "**调试**" 命令以正常**应用**模式（而不是一目了然或通知模式）启动。

当使用 Xcode 6 时，只有 iPhone 5、iPhone 5S、iPhone 6 和 iPhone 6 Plus 可以激活显示监视应用程序**Apple Watch-38mm**或**Apple Watch 42mm**的外部显示器。

> [!NOTE]
> 请记住，使用 Xcode 6 时，"监视" 屏幕不会自动出现在 iOS 模拟器中。
> 使用 "**硬件 > 外部显示**" 菜单来显示 "监视" 屏幕。

<a name="custommodes" />

## <a name="launching-notification-mode"></a>启动通知模式

有关如何在代码中处理通知的信息，请参阅 "[通知" 页](~/ios/watchos/platform/notifications.md)。

Visual Studio for Mac 可以使用通知_启动模式_为通知启动 watch 应用：

右键单击 "监视应用程序" 项目，然后选择 "**运行 > 自定义配置 ...** "：

[![](installation-images/runwith-customparams-sml.png "Running a Custom Configuration")](installation-images/runwith-customparams.png#lightbox)

这将打开 "**自定义参数**" 窗口，在其中可以选择 "**通知**" （并提供 JSON 负载），然后按 "**运行**" 以在模拟器中启动监视应用程序：

[![](installation-images/runwith-execargs-sml.png "Setting the Notification and Payload")](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>调试

在 Visual Studio for Mac 和 Visual Studio 支持调试。
请记住在通知模式下进行调试时提供的通知的 JSON 文件。 此屏幕截图显示调试断点命中在 watch 应用中：

![](installation-images/debug-sml.png "This screenshot shows a debug breakpoint being hit in a watch app")

遵循启动说明后，你将会看到在 IOS 模拟器上运行的监视应用 **（监视）** 。
对于通知模式，可以选择 "**调试" > 打开 "系统日志**（**CMD +/** ）" 并在代码中使用 `Console.WriteLine`。

### <a name="debugging-lifecycle-event-handlers"></a>调试生命周期事件处理程序

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

WatchOS 模板文件（如 `InterfaceController`、`ExtensionDelegate`、`NotificationController`和 `ComplicationController`）附带了已实现的必需生命周期方法。 添加 `Console.WriteLine` 调用并读取**应用程序输出**，以更好地了解事件生命周期。

## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [第一次观看应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 提示](https://developer.apple.com/watchkit/tips/)
