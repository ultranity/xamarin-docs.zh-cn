---
title: 检查器安装和要求
description: 本文档介绍如何安装 Xamarin Inspector 并讨论受支持的操作系统、Ide 和应用平台。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 19c4a15fb2490c7bace4798b0cb8e062b1379a04
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917597"
---
# <a name="inspector-installation-and-requirements"></a>检查器安装和要求

## <a name="download-and-installation"></a>下载和安装

# <a name="windows"></a>[Windows](#tab/windows)

1. 下载并安装[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) ，并选择 "**采用 .net 的移动开发**" 工作负荷。
1. [登录](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio)以启用你的企业订阅。
1. [检查](~/tools/inspector/inspect.md)自己的应用！

# <a name="macos"></a>[macOS](#tab/macos)

1. 下载并安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. [登录](https://docs.microsoft.com/visualstudio/mac/activation)以启用你的企业订阅。
1. [检查](~/tools/inspector/inspect.md)自己的应用！

-----

## <a name="requirements"></a>要求

### <a name="supported-operating-systems"></a>受支持的操作系统

- **Mac** -OS X 10.11 或更高版本
- **Windows** -windows 7 或更高版本（包含 Internet Explorer 11 或更高版本以及 .net 4.6.1 或更高版本）

### <a name="supported-ides"></a>支持的 Ide

- Visual Studio for Mac
- Visual Studio 2017 与 .NET 工作负载的**移动开发**

Live app 检查适用于企业客户。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支持的应用平台

|应用平台|IDE 支持|说明|
|--- |--- |--- |
|Mac|仅 Visual Studio for Mac 中支持|
|iOS|在 Visual Studio 2017 和 Visual Studio for Mac 中受支持| 链接器行为必须设置为 "**不链接**" （在 " **iOS 生成**项目选项" 下） |
|Android|在 Visual Studio 2017 和 Visual Studio for Mac 中受支持|必须针对 Android > = 4.0.3，并启用**fastdev** 。<br />必须使用 Google、Visual Studio 或 Xamarin Android 仿真程序。 Android 7 仿真程序此时可能不允许检查。|
|WPF|仅在 Visual Studio 2017 中受支持|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>报告 Bug

应通过 Visual Studio 直接报告 bug：

- **帮助 > 发送反馈 > 报告问题**

请包含以下所有信息：

### <a name="platform-version-information"></a>平台版本信息

此信息至关重要。

Visual Studio For Mac

- **Visual Studio > 关于 Visual Studio > 显示 > 复制信息的详细信息**
- 粘贴到 bug 报告中

Visual Studio

- **有关 Visual Studio > 复制信息的帮助 >**
- 告诉我们你的操作系统版本以及你是在运行32位还是64位 Windows。

### <a name="log-files"></a>日志文件

始终附加 IDE 和检查器客户端日志文件。

检查器客户端

- Mac：`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x 还可以直接从主菜单中选择查找器（macOS）或资源管理器（Windows）中的日志文件：

- **帮助 > 显示日志文件**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio "**输出**" 窗格中的内容可能也有信息。

### <a name="project-settings"></a>项目设置

如果可以附加要检查的项目的 **.csproj** ，这会非常有用。 这比询问单个设置更容易。

此外，请确认你处于 "调试" 配置中。

### <a name="selected-devices"></a>所选设备

对于 Android 和 iOS，必须知道要检查的设备，这一点非常重要。 我们需要知道：

- 显示在 IDE 中的设备名称
- 设备的操作系统版本
- Android：验证你是否正在使用 x86 模拟器
- Android：你使用什么模拟器平台？ Google 模拟器？ Visual Studio Android Emulator？ Xamarin Android Player？
- 您正在调试的应用程序是否出现并在设备中运行？
- 设备是否具有网络连接（通过 web 浏览器检查）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
