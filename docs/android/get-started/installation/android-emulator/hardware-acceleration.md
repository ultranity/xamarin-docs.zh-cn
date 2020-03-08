---
title: 通过硬件加速提高仿真器性能 (Hyper-V & HAXM)
description: 本文介绍了如何使用计算机的硬件加速功能最大限度提高 Android Emulator 的性能。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: a724a21dfffead307ca3d65d5ff134cf2d7c90db
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912462"
---
# <a name="hardware-acceleration-for-emulator-performance-hyper-v--haxm"></a>通过硬件加速提高仿真器性能 (Hyper-V & HAXM)

本文介绍了如何使用计算机的硬件加速功能最大限度提高 Android Emulator 的性能。 

Visual Studio 便于开发人员在无法使用 Android 设备的情况下通过使用 Android Emulator 来测试和调试 Xamarin.Android 应用程序。
但是，如果硬件加速在运行 Android 仿真器的计算机上不可用，那么它的运行速度太慢。 通过将特殊的 x86 虚拟设备映像与计算机的虚拟化功能结合使用，可以极大地提高 Android Emulator 的性能。

| 方案    | HAXM        | WHPX       | Hypervisor.Framework |
| ----------- | ----------- | -----------| ----------- |
| 有 Intel 处理器 | X | X | X |
| 有 AMD 处理器   |   | X |   |
| 想支持 Hyper-V |   | X |   |
| 想支持嵌套虚拟化 |   | 有限 |   |
| 想使用 Docker 等技术  |   | X | X |

::: zone pivot="windows"

## <a name="accelerating-android-emulators-on-windows"></a>在 Windows 上加速 Android Emulator

以下虚拟化技术可用于加速 Android Emulator：

1. **Microsoft 的 Hyper-V 和 Windows 虚拟机监控程序平台 (WHPX)** 。
   [Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/) 是 Windows 的虚拟化功能，使虚拟的计算机系统可以在物理主计算机上运行。

2. **Intel 硬件加速执行管理器 (HAXM)** 。
   HAXM 是运行 Intel CPU 的计算机所用的虚拟化引擎。

为了在 Windows 上获得最佳体验，建议使用 WHPX 来加速 Android Emulator。 如果计算机没有 Hyper-V，则可使用 HAXM。 如果满足以下条件，Android Emulator 将自动使用硬件加速：

- 硬件加速在开发计算机上可用并已启用。

- 仿真器正在运行为基于 x86 的虚拟设备创建的系统映像  。

> [!IMPORTANT]
> 不可在另一 VM（例如由 VirtualBox、VMWare 或 Docker 托管的 VM）内运行经过 VM 加速的仿真器。 必须[直接在系统硬件上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)运行 Android Emulator 。

有关使用 Android Emulator 进行启动和调试的信息，请参阅 [Android Emulator 调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

<a name="hyper-v-win" />

## <a name="accelerating-with-hyper-v"></a>使用 HYPER-V 加速

在启用 Hyper-V 之前，请阅读以下部分以验证你的计算机是否支持 Hyper-V。

### <a name="verifying-support-for-hyper-v"></a>验证对 Hyper-V 的支持

Hyper-V 在 Windows 虚拟机监控程序平台上运行。 若要将 Android Emulator 与 Hyper-V 配合使用，计算机必须满足以下条件才能支持 Windows 虚拟机监控程序平台：

- 计算机硬件必须满足以下要求：

  - 支持二级地址转换 (SLAT) 的 64 位 Intel 或 AMD Ryzen CPU。
  - CPU 支持 VM 监视器模式扩展（Intel CPU 的 VT-c 技术）。
  - 内存至少为 4 GB。

- 在计算机的 BIOS 中，必须启用以下项：

  - 虚拟化技术（标签可能因母板制造商而不同）。
  - 硬件强制执行数据执行保护。

- 计算机必须更新到 Windows 10 2018 年 4 月更新（版本 1803）或更高版本。 可使用以下步骤验证 Windows 版本是否为最新版本：

  1. 在 Windows 搜索框中，输入“关于”  。
  2. 在搜索结果中选择“关于你的电脑”  。
  3. 在“关于”对话框中向下滚动到“Windows 规范”部分   。
  4. 验证“版本”最低为 1803 版  ：

      [![Windows 规范](hardware-acceleration-images/win/01-about-windows-w10-sml.png)](hardware-acceleration-images/win/01-about-windows-w10.png#lightbox)

要验证计算机硬件和软件是否与 Hyper-V 兼容，请打开命令提示符并键入以下命令：

```cmd
systeminfo
```

如果列出的所有 Hyper-V 要求的值均为“是”，则计算机可以支持 Hyper-V  。 例如：

[![示例 Systeminfo 输出](hardware-acceleration-images/win/02-systeminfo-w158-sml.png)](hardware-acceleration-images/win/02-systeminfo-w158.png#lightbox)

### <a name="enabling-hyper-v-acceleration"></a>启用 HYPER-V 加速

若计算机符合上述条件，请执行以下步骤使用 Hyper-V 加速 Android Emulator：

1. 在 Windows 搜索框中输入“Windows 功能”，然后在搜索结果中选择“打开或关闭 Windows 功能”   。 在“Windows 功能”对话框中，启用“Hyper-V”和“Windows 虚拟机监控程序平台”    ：

    [![启用 Hyper-V 和 Windows 虚拟机监控程序平台](hardware-acceleration-images/win/03-hyper-v-settings-w158-sml.png)](hardware-acceleration-images/win/03-hyper-v-settings-w158.png#lightbox)

   进行这些更改后，重新启动计算机。
   
> [!IMPORTANT]
>
> 在 Windows 10 2018 年 10 月更新(RS5) 及更高版本中，你只需启用 Hyper-V 即可，因为它将自动使用 Windows 虚拟机监控程序平台 (WHPX)。

2. 安装 [Visual Studio 15.8 或更高版本](https://visualstudio.microsoft.com/vs/)（此版本 Visual Studio 通过 Hyper-V 提供用于运行 Android Emulator 的 IDE 支持）  。

3. **安装 Android Emulator 包 27.2.7 或更高版本**。 要安装此包，请在 Visual Studio 中导航到“工具”>“Android”>“Android SDK 管理器”  。 选择“工具”选项卡，确保 Android Emulator 版本至少为 27.2.7  。 另请确保 Android SDK Tools 版本为 26.1.1 或更高版本：

    [![“Android SDK 和工具”对话框](hardware-acceleration-images/win/04-sdk-manager-w158-sml.png)](hardware-acceleration-images/win/04-sdk-manager-w158.png#lightbox)

创建虚拟设备时（参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)），请确保选择基于 x86 的系统映像  。 如果使用基于 ARM 的系统映像，虚拟设备将无法加速并且运行缓慢。

## <a name="accelerating-with-haxm"></a>使用 HAXM 加速

若计算机不支持 Hyper-V，请使用 HAXM 加速 Android Emulator。 若要使用 HAXM，必须[禁用 Device Guard](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#disable-devguard)。

### <a name="verifying-haxm-support"></a>验证 HAXM 支持

要确定硬件是否支持 HAXM，请按照[我的处理器是否支持 Intel 虚拟化技术？](https://www.intel.com/content/www/us/en/support/processors/000005486.html)中的步骤操作。
若硬件支持 HAXM，可以使用以下步骤检查是否已安装 HAXM：

1. 打开命令提示符窗口，然后输入以下命令：

    ```cmd
    sc query intelhaxm
    ```

2. 检查输出，查看 HAXM 进程是否正在运行。 如果它正在运行，你会看到将 `intelhaxm` 状态列为 `RUNNING` 的输出。 例如：

    ![HAXM 可用时 sc 查询命令的输出](hardware-acceleration-images/win/05-sc_query-w158.png)

   如果未将 `STATE` 设置为 `RUNNING`，则未安装 HAXM。

如果计算机可以支持 HAXM 但未安装 HAXM，请按照下一部分中的步骤安装 HAXM。

<a name="install-haxm-win" />

### <a name="installing-haxm"></a>安装 HAXM

可以从 [Intel 硬件加速执行管理器](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager)页获取适用于 Windows 的 HAXM 安装包。 若要下载并安装 HAXM，请按照下列步骤操作：

1. 从 Intel 网站下载适用于 Windows 的最新 [HAXM 虚拟化引擎](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/)安装程序。 直接从 Intel 网站下载 HAXM 安装程序的优点是可以确保使用最新版本。

2. 运行 intelhaxm android.exe  ，启动 HAXM 安装程序。 接受安装程序对话框中的默认值：

   ![“Intel 硬件加速执行管理器安装程序”窗口](hardware-acceleration-images/win/06-haxm-installer.png)

创建虚拟设备时（参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)），请确保选择基于 x86 的系统映像  。 如果使用基于 ARM 的系统映像，虚拟设备将无法加速并且运行缓慢。

## <a name="troubleshooting"></a>疑难解答

有关解决硬件加速问题的帮助，请参阅 Android Emulator [疑难解答](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#accel-issues-win)指南。

::: zone-end
::: zone pivot="macos"

## <a name="accelerating-android-emulators-on-macos"></a>在 macOS 上加速 Android Emulator

以下虚拟化技术可用于加速 Android Emulator：

1. **Apple 的虚拟机监控程序框架**。
   [虚拟机监控程序](https://developer.apple.com/documentation/hypervisor)是 macOS 10.10 及更高版本的一项功能，可在 Mac 上运行虚拟机。

2. **Intel 硬件加速执行管理器 (HAXM)** 。
   [HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm) 是运行 Intel CPU 的计算机所用的虚拟化引擎。

建议使用虚拟机监控程序框架来加速 Android 仿真器。 如果 Mac 上没有虚拟机监控程序框架，则可以使用 HAXM。 如果满足以下条件，Android Emulator 将自动使用硬件加速：

- 硬件加速在开发计算机上可用并已启用。

- 仿真器正在运行为基于 x86 的虚拟设备创建的系统映像  。

> [!IMPORTANT]
>
> 不可在另一 VM（例如由 VirtualBox、VMWare 或 Docker 托管的 VM）内运行经过 VM 加速的仿真器。 必须[直接在系统硬件上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)运行 Android Emulator 。

有关使用 Android Emulator 进行启动和调试的信息，请参阅 [Android Emulator 调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

<a name="hypervisor" />

## <a name="accelerating-with-the-hypervisor-framework"></a>使用虚拟机监控程序框架加速

要将 Android Emulator 与虚拟机监控程序框架配合使用，Mac 必须符合以下条件：

- Mac 必须运行 macOS 10.10 或更高版本。

- Mac 的 CPU 必须能够支持虚拟机监控程序框架。

如果你的 Mac 符合这些条件，Android 仿真器将自动使用虚拟机监控程序框架进行加速。 若不确定 Mac 是否支持虚拟机监控程序框架，请参阅[疑难解答](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#hypervisor-issues)指南，了解验证 Mac 是否支持虚拟机监控程序的方法。

如果 Mac 不支持虚拟机监控程序框架，可以使用 HAXM 加速 Android Emulator（如下所述）。

<a name="haxm-mac" />

## <a name="accelerating-with-haxm"></a>使用 HAXM 加速

若你的 Mac 不支持虚拟机监控程序框架（或者 macOS 版本低于 10.10），则可使用“Intel 的硬件加速执行管理器”([HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)) 来加速 Android Emulator  。

在首次将 HAXM 与 Android Emulator 配合使用之前，最好先验证 HAXM 是否已安装并可供 Android Emulator 使用。

### <a name="verifying-haxm-support"></a>验证 HAXM 支持

可使用以下步骤检查是否已安装 HAXM：

1. 打开终端，然后输入以下命令：

    ```bash
    ~/Library/Developer/Xamarin/android-sdk-macosx/tools/emulator -accel-check
    ```

   此命令假定将 Android SDK 安装在默认位置“~/Library/Developer/Xamarin/android-sdk-macosx”；否则，请在 Mac 上修改 Android SDK 位置的路径  。

2. 若已安装 HAXM，则上面的命令将返回类似于以下结果的消息：

    ```bash
    HAXM version 7.2.0 (3) is installed and usable.
    ```

   若 HAXM 未安装，则返回类似于以下输出的消息  ：

    ```bash
    HAXM is not installed on this machine (/dev/HAX is missing).
    ```

如果未安装 HAXM，请按照下一部分的步骤安装 HAXM。

<a name="install-haxm-mac" />

### <a name="installing-haxm"></a>安装 HAXM

可以从 [Intel 硬件加速执行管理器](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager)页获取适用于 macOS 的 HAXM 安装包。 若要下载并安装 HAXM，请按照下列步骤操作：

1. 从 Intel 网站下载适用于 macOS 的最新 [HAXM 虚拟化引擎](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/)安装程序。

2. 运行 HAXM 安装程序。 接受安装程序对话框中的默认值：

   [![“Intel 硬件加速执行管理器安装程序”窗口](hardware-acceleration-images/mac/01-haxm-installer-sml.png)](hardware-acceleration-images/mac/01-haxm-installer.png#lightbox)

## <a name="troubleshooting"></a>疑难解答

有关解决硬件加速问题的帮助，请参阅 Android Emulator [疑难解答](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#accel-issues-mac)指南。

::: zone-end

## <a name="related-links"></a>相关链接

- [在 Android Emulator 上运行应用](https://developer.android.com/studio/run/emulator)
