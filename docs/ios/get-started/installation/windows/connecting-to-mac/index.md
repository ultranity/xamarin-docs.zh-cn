---
title: 通过“与 Mac 配对”进行 Xamarin.iOS 开发
description: 本指南介绍如何使用“与 Mac 配对”将 Visual Studio 2019 连接到 Mac 生成主机。 其中讨论了如何启用 Mac 远程登录、如何从 Visual Studio 2019 连接到 Mac、以及如何将 Mac 生成主机手动添加到 Windows 计算机等。
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: 81377d790d695b60465319aeb166eb1e62483211
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940931"
---
# <a name="pair-to-mac-for-xamarinios-development"></a>通过“与 Mac 配对”进行 Xamarin.iOS 开发

本指南介绍如何使用“与 Mac 配对”将 Visual Studio 2019 连接到 Mac 生成主机。这些说明也适用于 Visual Studio 2017。

## <a name="overview"></a>概述

生成本机 iOS 应用程序需要使用仅在 Mac 上运行的 Apple 生成工具。 因此，Visual Studio 2019 必须连接到可访问网络的 Mac，以生成 Xamarin.iOS 应用程序。

Visual Studio 2019 的“与 Mac 配对”功能会发现、连接到 Mac 生成主机、对其进行验证并记住生成主机，以便基于 Windows 的 iOS 开发人员能够高效工作。

“与 Mac 配对”启用以下开发工作流：

- 开发人员可在 Visual Studio 2019 中编写 Xamarin.iOS 代码。

- Visual Studio 2019 会建立与 Mac 生成主机的连接，并使用该计算机上的生成工具来编译 iOS 应用并对应用进行签名。

- 无需在 Mac 上单独运行应用程序 – Visual Studio 2019 通过 SSH 安全调用 Mac 生成。

- 发生更改时将立刻通知 Visual Studio 2019。 例如，将某一 iOS 设备插入 Mac 时，或该设备在网络上可用时，iOS 工具栏会立即更新。

- Visual Studio 2019 的多个实例可同时连接到 Mac。

- 可使用 Windows 命令行生成 iOS 应用程序。

> [!NOTE]
>
> 按照本指南中的说明进行操作之前，请完成以下步骤：
>
> - 在 Windows 计算机上，[安装 Visual Studio 2019](~/get-started/installation/windows.md)
> - 在 Mac 上，[安装 Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) 和 [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)
>   - _安装后必须手动打开 Xcode_，以便它可以添加任何其他的组件。
>
> 如果不想安装 Visual Studio for Mac，Visual Studio 2019 可以使用 Xamarin.iOS 和 Mono 自动配置 Mac 生成主机。
> 仍然需安装和运行 Xcode。
> 有关详细信息，请参阅[自动预配 Mac](#automatic-mac-provisioning)。

## <a name="enable-remote-login-on-the-mac"></a>启用 Mac 上的远程登录

若要设置 Mac 生成主机，首先启用远程登录：

1. 在 Mac 上，打开“系统首选项”并转到“共享”窗格。

2. 选中“服务”列表中的“远程登录”。

    ![启用远程登录](images/sharing.png "启用远程登录")

    确保“远程登录”配置为“允许所有用户”访问，或确保 Mac 用户名或组包含在允许用户列表中。

3. 如果出现提示，请配置 macOS 防火墙。

    如果将 macOS 防火墙默认设置为阻止传入的连接，则可能需要允许 `mono-sgen` 接收传入的连接。 若出现此情况，系统将显示警报以进行提示。

4. 如果和 Windows 计算机位于同一网络上，则 Visual Studio 2019 现应可发现 Mac。 如果仍无法发现 Mac，请尝试[手动添加 Mac](#manually-add-a-mac) 或查看[疑难解答指南](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)。

## <a name="connect-to-the-mac-from-visual-studio-2019"></a>从 Visual Studio 2019 连接到 Mac

启用远程连接后，将 Visual Studio 2019 连接到 Mac。

1. 在 Visual Studio 2019 中，打开现有的 iOS 项目，或者通过选择“文件”>“新建”>“项目”，然后选择 iOS 项目模板来创建新的 iOS 项目。

2. 打开“与 Mac 配对”对话框。

    - 使用“与 Mac 配对”按钮 iOS 工具栏：

      ![iOS 工具栏，其中突出显示了“与 Mac 配对”按钮](images/ios-toolbar.png "iOS 工具栏，其中突出显示了“与 Mac 配对”按钮")

    - 或者，选择“工具”>“iOS”>“与 Mac 配对”。

    - “与 Mac 配对”对话框显示所有之前连接的和当前可用的 Mac 生成主机的列表：

      ![“与 Mac 配对”对话框](images/pairtomac.png "“与 Mac 配对”对话框")

3. 选择列表中的 Mac。 单击“连接” 。

4. 输入用户名和密码。

    - 第一次连接到某特定 Mac 时，会提示为该计算机输入用户名和密码：

      ![为 Mac 输入用户名和密码](images/auth.png "为 Mac 输入用户名和密码")

      > [!TIP]
      > 登录时，使用系统用户名而不是全名。

    - “与 Mac 配对”使用这些凭据创建到 Mac 的新 SSH 连接。 如果成功，会向 Mac 上的 authorized_keys 文件中添加一个密钥。 以后连接该 Mac 时会自动登录。

5. “与 Mac 配对”会自动配置 Mac。

    [从 Visual Studio 2019 版本 15.6 开始](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning)，Visual Studio 2019 会根据需要在已连接的 Mac 生成主机上安装或更新 Mono 和 Xamarin.iOS（请注意，仍须手动安装 Xcode）。 有关详细信息，请参阅[自动预配 Mac ](#automatic-mac-provisioning)。

6. 查找连接状态图标。

    - Visual Studio 2019 连接到 Mac 时，“与 Mac 配对”对话框中该 Mac 的项会显示一个图标，指示当前已连接该设备：

      ![已连接的 Mac](images/connected.png "已连接的 Mac")

      一次只能连接一个 Mac。

      > [!TIP]
      > 右键单击“与 Mac 配对”列表中的任意 Mac 会打开上下文菜单，可在菜单中选择“连接...”、“忘记此 Mac”或“断开连接”：
      >
      > ![“与 Mac 配对”上下文菜单](images/contextmenu.png "“与 Mac 配对”上下文菜单")
      >
      > 如果选择“忘记此 Mac”，系统将忘记所选 Mac 的凭据。 若要重新连接到此 Mac，将需要重新输入用户名和密码。

如果已成功配对 Mac 生成主机，则可以在 Visual Studio 2019 中生成 Xamarin.iOS 应用。 查看 [Xamarin.iOS for Visual Studio 简介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)指南。

如果尚无法配对 Mac，请尝试[手动添加 Mac](#manually-add-a-mac) 或查看[疑难解答指南](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)。

## <a name="manually-add-a-mac"></a>手动添加 Mac

如果未看到“与 Mac 配对”对话框中列出某特定 Mac，可手动添加：

1. 查找 Mac 的 IP 地址。

    - 在 Mac 上打开“系统首选项”>“共享”>“远程登录”：

      [![“系统首选项”>“共享”中的 Mac IP 地址](images/sharing-ipaddress.png "“系统首选项”>“共享”中的 Mac IP 地址")](images/sharing.png#lightbox)

    - 或者，使用命令行。 在终端，发出此命令：

      ```bash
      $ ipconfig getifaddr en0
      196.168.1.8
      ```

      根据网络配置，可能需要使用接口名称而不是 `en0`。 例如：`en1`、`en2` 等。

2. 在 Visual Studio 2019 的“与 Mac 配对”对话框中，选择“添加 Mac...”：

    [![“与 Mac 配对”对话框中的“添加 Mac”按钮](images/addtomac.png "“与 Mac 配对”对话框中的“添加 Mac”按钮")](images/addtomac-large.png#lightbox)

3. 输入 Mac 的 IP 地址并单击“添加”：

    ![输入 Mac 的 IP 地址](images/enteripaddress.png "输入 Mac 的 IP 地址")

4. 为 Mac 输入用户名和密码：

    ![输入用户名和密码](images/auth.png "输入用户名和密码")

   > [!TIP]
   > 登录时，使用系统用户名而不是全名。

5. 单击“登录”，通过 SSH 将 Visual Studio 2019 连接到 Mac，并将其添加到已知计算机列表。

## <a name="automatic-mac-provisioning"></a>自动预配 Mac

自 [Visual Studio 2019 版本 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) 起，与 Mac 配对后会使用构建 Xamarin.iOS 应用程序所必需的软件来自动预配 Mac：Mono、Xamarin.iOS（软件框架，而不是 Visual Studio for Mac IDE），以及与 Xcode 相关的工具（但不是 Xcode 本身）。

> [!IMPORTANT]
>
> - “与 Mac 配对”不能安装 Xcode；必须手动将其安装在 Mac 生成主机上。 它是 Xamarin.iOS 开发所必需的。
> - 自动预配 Mac 需要在 Mac 上启用远程登录，并且 Windows 计算机必须能够通过网络访问该 Mac。 有关详细信息，请参阅[在 Mac 上启用远程登录](#enable-remote-login-on-the-mac)。
> - 自动预配 Mac 需要 Mac 上有 3 GB 的可用空间用于安装 Xamarin.iOS。

Visual Studio 2019 [连接到 Mac](#connect-to-the-mac-from-visual-studio-2019) 时，“与 Mac 配对”会执行必要的软件安装/更新。

### <a name="mono"></a>Mono

“与 Mac 配对”将进行检查以确保已安装 Mono。 如果未安装，“与 Mac 配对”将在 Mac 上下载并安装最新且稳定的 Mono 版本。

安装过程中会出现各种提示，用于指示进度，如以下屏幕截图所示（点击放大）：

||安装检查|下载|安装
|---|---|---|---|
|Mono|[![缺失 Mono 安装](images/mono-missing.png "缺失 Mono 安装")](images/mono-missing-large.png#lightbox)|[![下载 Mono](images/mono-downloading.png "下载 Mono")](images/mono-downloading-large.png#lightbox)|[![安装 Mono](images/mono-installing.png "安装 Mono")](images/mono-installing-large.png#lightbox)|

### <a name="xamarinios"></a>Xamarin.iOS

“与 Mac 配对”升级 Mac 上的 Xamarin.iOS，以匹配 Windows 计算机上安装的版本。

> [!IMPORTANT]
> “与 Mac 配对”不会把 Mac 上的 Xamarin.iOS 从 alpha/beta 版本降级为稳定版本。 如果已安装 Visual Studio for Mac，请设置[发布通道](https://docs.microsoft.com/visualstudio/mac/update)，如下所示：
>
> - 如果使用 Visual Studio 2019，选择 Visual Studio for Mac 中的“稳定”更新通道。
> - 如果使用 Visual Studio 2019 预览版，选择 Visual Studio for Mac 中的“Alpha”更新通道。

安装过程中会出现各种提示，用于指示进度，如以下屏幕截图所示（点击放大）：

||安装检查|下载|安装
|---|---|---|---|
|Xamarin.iOS|[![缺失 Xamarin.iOS 安装](images/xamios-missing.png "缺失 Xamarin.iOS 安装")](images/xamios-missing-large.png#lightbox)|[![下载 Xamarin.iOS](images/xamios-downloading.png "下载 Xamarin.iOS")](images/xamios-downloading-large.png#lightbox)|[![安装 Xamarin.iOS](images/xamios-installing.png "安装 Xamarin.iOS")](images/xamios-installing-large.png#lightbox)|

### <a name="xcode-tools-and-license"></a>Xcode 工具和许可证

“与 Mac 配对”还检查以确定是否已安装 Xcode 并接受其许可证。 “与 Mac 配对”不安装 Xcode，但会提示接受许可证，如以下屏幕截图所示（点击放大）：

||安装检查|许可证接受|
|---|---|---|
|Xcode|[![缺失 Xcode 安装](images/xcode-missing.png "缺失 Xcode 安装")](images/xcode-missing-large.png#lightbox)|[![Xcode 许可证](images/xcode-license.png "Xcode 许可证")](images/xcode-license-large.png#lightbox)|

此外，“与 Mac 配对”会安装或更新使用 Xcode 分发的各种包。 例如：

- **MobileDeviceDevelopment.pkg**
- **XcodeExtensionSupport.pkg**
- **MobileDevice.pkg**
- **XcodeSystemResources.pkg**

这些包安装迅速，不出现提示。

> [!NOTE]
> 这些工具不同于 Xcode 命令行工具，从 macOS 10.9 开始，后者[与 Xcode 一并安装](https://developer.apple.com/library/content/technotes/tn2339/_index.html)。

### <a name="troubleshooting-automatic-mac-provisioning"></a>自动预配 Mac 疑难解答

如果在使用“自动预配 Mac”的过程中遇到任何问题，请查看存储在 %LOCALAPPDATA%\Xamarin\Logs\16.0 中的 Visual Studio 2019 IDE 日志。 这些日志可能包含错误消息，可帮助更好地诊断失败或获取支持。

## <a name="build-ios-apps-from-the-windows-command-line"></a>从 Windows 命令行生成 iOS 应用

“与 Mac 配对”支持从命令行生成 Xamarin.iOS 应用程序。 例如：

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

上面的示例中传递到 `msbuild` 的参数有：

- `ServerAddress` - Mac 生成主机的 IP 地址。
- `ServerUser` – 登录到 Mac 生成主机时要使用的用户名。
  使用系统用户名而不是全名。
- `ServerPassword` – 登录到 Mac 生成主机时要使用的密码。

> [!NOTE]
> Visual Studio 2019 将 `msbuild` 存储到以下目录中：**C:\Program Files (x86)\Microsoft Visual Studio\2019\\&lt;Version&gt;\MSBuild\Current\Bin**

“与 Mac 配对”第一次从 Visual Studio 2019 或命令行登录到特定的 Mac 生成主机时，会设置 SSH 密钥。 借助这些密钥，之后登录时无需使用用户名或密码。 新创建的密钥存储在 %LOCALAPPDATA%\Xamarin\MonoTouch。

如果从命令行生成调用中省略 `ServerPassword` 参数，“与 Mac 配对”会尝试使用保存的 SSH 密钥登录到 Mac 生成主机。

## <a name="summary"></a>总结

本文介绍如何使用“与 Mac 配对”功能将 Visual Studio 2019 连接到 Mac 生成主机，以便 Visual Studio 2019 开发人员能使用 Xamarin.iOS 生成本机 iOS 应用程序。

## <a name="next-steps"></a>后续步骤

- [连接疑难解答](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Xamarin Mac 生成代理视频](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
- [Xamarin.iOS for Visual Studio 简介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [适用于 Windows 的远程 iOS 模拟器](~/tools/ios-simulator/index.md)
- [无线部署](~/ios/deploy-test/wireless-deployment.md)
