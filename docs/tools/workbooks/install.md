---
title: 工作簿安装和要求
description: 本文档介绍如何下载和安装 Xamarin Workbooks，并讨论支持的平台和系统要求。
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 92b08a9cfd725c1114b71fad4f29e6f28cd828b3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914386"
---
# <a name="workbooks-installation-and-requirements"></a>工作簿安装和要求

<a name="install" />

## <a name="download-and-install"></a>下载并安装

<!-- markdownlint-disable MD001 -->

# <a name="windows"></a>[Windows](#tab/windows)

1. 请检查以下[要求](#requirements)。
2. 下载并安装[适用于 Windows 的 Xamarin Workbooks](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
3. 开始[围绕](~/tools/workbooks/workbook.md)工作簿播放。

# <a name="macos"></a>[macOS](#tab/macos)

1. 请检查以下[要求](#requirements)。
2. 下载并安装[适用于 Mac 的 Xamarin Workbooks](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
3. 开始[播放](~/tools/workbooks/workbook.md)。

-----

## <a name="requirements"></a>要求

#### <a name="supported-operating-systems"></a>受支持的操作系统

- **Mac** -OS X 10.11 或更高版本
- **Windows** -windows 7 或更高版本（包含 Internet Explorer 11 或更高版本以及 .net 4.6.1 或更高版本）

#### <a name="supported-app-platforms"></a>支持的应用平台

|应用平台|OS 支持|说明|
|--- |--- |--- |
|Mac|仅在 Mac 上受支持|
|iOS|在 Mac 和 Windows 上受支持|需要在 Mac 上安装 Xamarin 11.0 和 Xcode 9.0 或更高版本。 在 Windows 上运行 iOS 工作簿需要运行上述所有的 Mac 生成主机，以及在 Windows 上安装的[远程 IOS 模拟器](~/tools/ios-simulator/index.md)。|
|Android|在 Mac 和 Windows 上受支持|必须使用 Google、Visual Studio 或 Xamarin Android 模拟器，并且虚拟设备 > = 5。0|
|WPF|仅在 Windows 上受支持|
|控制台（.NET Framework）|在 Mac 和 Windows 上受支持|
|控制台（.NET Core）|在 Mac 和 Windows 上受支持|

## <a name="reporting-bugs"></a>报告 Bug

请[在 GitHub 上报告问题][bugs]，并提供以下信息：

### <a name="log-files"></a>日志文件

始终附加工作簿客户端日志文件：

- Mac：`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x 还可以直接从主菜单中选择查找器（macOS）或资源管理器（Windows）中的日志文件：

- **帮助 > 显示日志文件**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>工作簿1.3 及更早版本的日志路径：

- Mac：`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>平台版本信息

了解有关操作系统和已安装 Xamarin 产品的详细信息非常有用。

从工作簿中的主菜单：

- **帮助 > 复制版本信息**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>工作簿1.3 及更早版本的说明：

Visual Studio For Mac

- **Visual Studio > 关于 Visual Studio > 显示 > 复制信息的详细信息**
- 粘贴到 bug 报告中

Visual Studio

- **有关 Visual Studio > 复制信息的帮助 >**
- 告诉我们你的操作系统版本以及你是在运行32位还是64位 Windows。

### <a name="samples"></a>示例

如果您可以附加或链接到您遇到的**工作簿**文件，则可能有助于更快地解决您的 bug。

### <a name="devices"></a>设备

如果在连接 iOS 或 Android 工作簿时遇到问题，并且已经检查了[疑难解答页面](~/tools/workbooks/troubleshooting/index.md)，我们需要知道：

- 要尝试连接到的设备的名称
- 设备的操作系统版本
- Android：验证你是否正在使用 x86 模拟器
- Android：你使用什么模拟器平台？ Google 模拟器？
  Visual Studio Android Emulator？ Xamarin Android Player？
- Windows 上的 iOS：你已安装的 Xamarin 远程 iOS 模拟器的版本（检查**控制面板**中的 "**添加/删除程序**"）？
- Windows 上的 iOS：还提供 Mac 生成主机的平台版本信息
- 设备是否具有网络连接（通过 web 浏览器检查）？

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>卸载

### <a name="windows"></a>Windows

根据您获取工作簿的方式，您可能需要执行两个卸载过程。 请检查这两项以完全卸载软件。

#### <a name="visual-studio-installer"></a>Visual Studio 安装程序

如果你安装了 Visual Studio 2017，请打开**Visual Studio 安装程序**，并在**单个组件**中查找**Xamarin Workbooks**。 如果已选中，则取消选中它，然后单击 "**修改**" 以卸载。

#### <a name="system-uninstall"></a>系统卸载

如果你使用已下载的安装程序自行安装了工作簿，则需要通过 Windows 10 上的 "**应用 & 功能**" 系统设置 "页上的" 应用 "或通过" 控制面板 "上的" 控制面板 "中的"**添加/删除程序**"将其卸载。

> **> 系统 > 应用程序 & 功能启动 > 设置**

![](install-images/windows-remove.png "Xamarin Workbooks as listed in &quot;Apps &amp; features&quot;")

**你仍应按照 Visual Studio 安装程序的过程进行操作，以确保在不知情的情况下不会重新安装工作簿。**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

从[1.2.2](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/interactive/interactive-1.2.md)开始，可以通过运行以下内容从终端卸载 Xamarin Workbooks：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

卸载程序将详细说明它将删除的文件和目录，并在继续操作之前要求确认。

对于更高级的方案，将 `-help` 参数传递到 `uninstall` 脚本。

对于旧版本，需手动删除以下各项：

1. 在 `"/Applications/Xamarin Workbooks.app"` 删除 Workbooks 应用
2. 在 `"Applications/Xamarin Inspector.app"` 删除 Inspector 应用
3. 删除加载项：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. 在 `/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework` 删除 Inspector 和支持文件

## <a name="downgrading"></a>降级

**/Applications/Xamarin 工作簿**的捆绑标识符将从 `com.xamarin.Inspector` 更改为1.4 版本中的 `com.xamarin.Workbooks`，因为工作簿和检查器现已完全拆分。

由于较旧的安装程序中的 bug，无法使用1.3.2 或较旧的安装程序降级1.4 或更高版本。

从1.4 或更高版本降级到1.3.2 或更高版本：

1. [手动卸载工作簿 & 检查器](#uninstall-macos)
2. 运行1.3.2 或更早 `.pkg` 安装程序
