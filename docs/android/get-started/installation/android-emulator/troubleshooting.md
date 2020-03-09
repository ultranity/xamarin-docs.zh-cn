---
title: Android Emulator 疑难解答
description: 本文介绍如何诊断和避开使用 Android Emulator. 时可能发生的问题。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: d73c72fa70a22bacf122f5c3957b789914dfd765
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912377"
---
# <a name="android-emulator-troubleshooting"></a>Android Emulator 疑难解答

本文介绍配置和运行 Android Emulator 时最常见的警告消息和问题。此外，其中还介绍了这些错误的解决方案以及各种疑难解答提示，以帮助诊断仿真器出现的问题。

::: zone pivot="windows"

## <a name="deployment-issues-on-windows"></a>在 Windows 上部署时出现的问题

部署应用时，仿真器可能会显示一些错误消息。 此处介绍最常见的错误和解决方案。

### <a name="deployment-errors"></a>部署错误

若出现有关无法在仿真器上安装 APK 或无法运行 Android Debug Bridge (adb) 的错误消息，请验证 Android SDK 能否连接到仿真器  。 要验证仿真器的连接情况，请使用以下步骤：

1. 通过“Android Device Manager”启动仿真器（选择虚拟设备并单击“启动”）   。

2. 打开命令提示符，转到 adb 的安装文件夹  。 如果 Android SDK 安装在其默认位置，则 adb 位于 C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools\\adb.exe；如果不是，请在计算机上修改 Android SDK 所在的路径   。

3. 键入以下命令：

   ```shell
   adb devices
   ```

4. 如果可以通过 Android SDK 访问仿真器，那么仿真器应该就显示在附加设备列表中。 例如：

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. 如果仿真器不在此列表中，请启动“Android SDK 管理器”  ，应用所有更新，再尝试重启仿真器。

### <a name="mmio-access-error"></a>MMIO 访问错误

若出现“发生 MMIO 访问错误”消息，请重启仿真器  。

<a name="gps-win" />

## <a name="missing-google-play-services"></a>缺少 Google Play Services

如果在仿真器中运行的虚拟设备未安装 Google Play Services 或 Google Play 商店，则在未安装这些软件包时创建虚拟设备通常会出现此情况。 创建虚拟设备时（请参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)），请务必选择以下一个或两个选项：

- Google API  &ndash; 在虚拟设备中包含 Google Play 服务。
- Google Play 商店  &ndash; 在虚拟设备中包含 Google Play 商店。

例如，此虚拟设备将包含 Google Play Services 和 Google Play 商店：

[![启用了 Google Play Services 和 Google Play 商店的示例 AVD](troubleshooting-images/win/00-add-gps-w158-sml.png)](troubleshooting-images/win/00-add-gps-w158.png#lightbox)

> [!NOTE]
> Google Play 商店图片仅适用于某些基本设备类型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

<a name="perf-win" />

## <a name="performance-issues"></a>性能问题

性能问题通常由以下某个问题引起：

- 仿真器在没有硬件加速的情况下运行。

- 在仿真器中运行的虚拟设备未使用基于 x86 的系统映像。

以下部分更详细地介绍了这些方案。

### <a name="hardware-acceleration-is-not-enabled"></a>未启用硬件加速

如果未启用硬件加速，则从设备管理器启动虚拟设备时将生成一个对话框，其中显示一条错误消息，指出未正确配置 Windows 虚拟机监控程序平台 (WHPX)：

![示例设备管理器警告](troubleshooting-images/win/01-dev-mgr-warning-w158.png)

如果显示此错误消息，请参阅下面的[硬件加速问题](#accel-issues-win)，了解可用于验证和启用硬件加速的步骤。

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>加速已启用但仿真器运行速度过慢 

导致此问题的常见原因是虚拟设备 (AVD) 中未使用基于 x86 的映像。 创建虚拟设备时（请参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)），请确保选择基于 x86 的系统映像：

[![为虚拟设备选择 x86 系统映像](troubleshooting-images/win/02-x86-virtual-device-w158-sml.png)](troubleshooting-images/win/02-x86-virtual-device-w158.png#lightbox)

<a name="accel-issues-win" />

## <a name="hardware-acceleration-issues"></a>硬件加速问题

无论使用 Hyper-V 还是 HAXM 进行硬件加速，都可能会遇到配置问题或与计算机上的其他软件发生冲突。 可通过打开命令提示符并输入以下命令来验证是否已启用硬件加速（以及仿真器正在使用哪种加速方法）：

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator-check.exe" accel
```

此命令假定将 Android SDK 安装在默认位置 C:\\Program Files (x86)\\Android\\android-sdk；如果不是，请在计算机上修改上述 Android SDK 所在的路径  。

### <a name="hardware-acceleration-not-available"></a>硬件加速不可用

如果 Hyper-V 可用，将从 emulator-check.exe accel 命令返回类似以下示例的消息  ：

```cmd
HAXM is not installed, but Windows Hypervisor Platform is available.
```

如果 HAXM 可用，将返回类似以下示例的消息：

```cmd
HAXM version 6.2.1 (4) is installed and usable.
```

如果硬件加速不可用，将显示如下示例的消息（如果无法找到 Hyper-V，仿真器将查找 HAXM）：

```cmd
HAXM is not installed on this machine
```

如果硬件加速不可用，请参阅[使用 Hyper-V 加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vswin#hyper-v-win)了解如何在计算机上启用硬件加速。

### <a name="incorrect-bios-settings"></a>BIOS 设置不正确

如果未正确配置 BIOS 以支持硬件加速，则在运行 emulator-check.exe accel 命令时将显示类似于以下示例的消息  ：

```cmd
VT feature disabled in BIOS/UEFI
```

要解决此问题，请重启计算机的 BIOS 并启用以下选项：

- 虚拟化技术（标签可能因母板制造商而不同）。
- 硬件强制执行数据执行保护。

如果启用了硬件加速并且 BIOS 配置正确，则仿真器可通过硬件加速成功运行。
然而，一些特定于 Hyper-V 和 HAXM 的问题仍可能引起某些问题，下面将对此进行解释。

### <a name="hyper-v-issues"></a>HYPER-V 问题

在某些情况下，在“打开或关闭 Windows 功能”对话框中启用 Hyper-V 和 Windows 虚拟机监控程序平台后可能无法正确启用 Hyper-V    。 要验证是否已启用 Hyper-V，请使用以下步骤：

1. 在 Windows 搜索框中，输入“powershell”  。

2. 右键单击搜索结果中的 Windows PowerShell，然后选择“以管理员身份运行”   。

3. 在 PowerShell 控制台中，输入以下命令：

    ```powershell
    Get-WindowsOptionalFeature -FeatureName Microsoft-Hyper-V-All -Online
    ```

    如果未启用 Hyper-V，将显示类似于以下示例的消息，指示 Hyper-V 的状态为“已禁用”  ：

    ```
    FeatureName      : Microsoft-Hyper-V-All
    DisplayName      : Hyper-V
    Description      : Provides services and management tools for creating and running virtual machines and their resources.
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

4. 在 PowerShell 控制台中，输入以下命令：

    ```powershell
    Get-WindowsOptionalFeature -FeatureName HypervisorPlatform -Online
    ```

    如果未启用虚拟机监控程序，将显示类似于以下示例的消息，指示虚拟机监控程序平台的状态为“已禁用”  ：

    ```
    FeatureName      : HypervisorPlatform
    DisplayName      : Windows Hypervisor Platform
    Description      : Enables virtualization software to run on the Windows hypervisor
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

如果未启用 Hyper-V 和/或虚拟机监控程序平台，请使用以下 PowerShell 命令进行启用：

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
```

完成这些命令后，请进行重启。 

有关启用 Hyper-V 的详细信息（包括使用部署映像服务和管理工具启用 Hyper-V 的技术），请参阅[安装 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。

### <a name="haxm-issues"></a>HAXM 问题

导致 HAXM 问题的原因通常包括：与其他虚拟化技术冲突、设置不正确或 HAXM 驱动器不是最新版本。

### <a name="haxm-process-is-not-running"></a>HAXM 进程未运行

如果已安装 HAXM，则可通过打开命令提示符并输入以下命令来验证 HAXM 进程是否正在运行：

```cmd
sc query intelhaxm
```

如果 HAXM 进程正在运行，应看到类似于下面结果的输出：

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```

如果 `STATE` 未设置为 `RUNNING`，请参阅[如何使用 Intel 硬件加速执行管理器](https://software.intel.com/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator)解决该问题。

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>HAXM 虚拟化冲突

HAXM 可能与其他使用虚拟化的技术（如 Hyper-V、Windows Device Guard 和某防病毒软件）冲突：

- Hyper-V  &ndash; 如果使用的是 Windows 10 2018 年 4 月更新（内部版本 1803）之前的 Windows 版本，并启用了 Hyper-V，请按照[禁用 Hyper-V](#disable-hyperv) 中的步骤操作，以便启用 HAXM  。

- Device Guard  &ndash; Device Guard 和 Credential Guard 可阻止在 Windows 计算机上禁用 Hyper-V。 若要禁用 Device Guard 和 Credential Guard，请参阅[禁用 Device Guard](#disable-devguard)。

- 防病毒软件  &ndash; 如果运行的防病毒软件（如 Avast）使用硬件协助虚拟化，请禁用或卸载此软件，再重启并重试运行 Android 仿真器。

#### <a name="incorrect-bios-settings"></a>BIOS 设置不正确

若要在 Windows PC 上使用 HAXM，只有在 BIOS 中启用虚拟化技术 (Intel VT-x)，HAXM 才能正常运行。 如果已禁用 VT-x ，则尝试启用 Android Emulator 时会出现如下错误消息：

**此计算机符合 HAXM 要求，但未启用 Intel 虚拟化技术 (VT-x)。**

若要更正此错误，请将计算机引导到 BIOS，同时启用 VT-x 和 SLAT（第二级地址转换），再重启计算机，以返回到 Windows。

<a name="disable-hyperv" />

#### <a name="disabling-hyper-v"></a>禁用 Hyper-V

如果使用的是 Windows 10 2018 年 4 月更新（内部版本 1803）之前的 Windows 版本，并启用了 Hyper-V，则必须禁用 Hyper-V 并重启计算机才能安装和使用 HAXM  。 如果使用的是 Windows 10 2018 年 4 月更新（内部版本 1803）或更高版本，则 Android Emulator 27.2.7 或更高版本可以使用 Hyper-V（而不是 HAXM）进行硬件加速，因此不需要禁用 Hyper-V  。

可以按照下列步骤操作，在“控制面板”中禁用 Hyper-V：

1. 在 Windows 搜索框中输入“Windows 功能”，然后在搜索结果中选择“打开或关闭 Windows 功能”   。

2. 取消选中 Hyper V  ：

    ![在“Windows 功能”对话框中禁用 Hyper-V](troubleshooting-images/win/03-uncheck-hyper-v.png)

3. 重新启动计算机。

或者，可使用以下 PowerShell 命令禁用 Hyper-V 虚拟机监控程序：

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM 和 Microsoft Hyper-V 不能同时处于活动状态。 遗憾的是，没有办法在不重启计算机的情况下在 Hyper-V 和 HAXM 之间切换。 

在某些情况下，如果启用了 Device Guard 和 Credential Guard，按照前述步骤操作将无法成功禁用 Hyper-V。 如果无法禁用 Hyper-V（或似乎已禁用，但仍无法安装 HAXM），请按照下一部分中的步骤操作，禁用 Device Guard 和 Credential Guard。

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>禁用 Device Guard

Device Guard 和 Credential Guard 可阻止在 Windows 计算机上禁用 Hyper-V。 对于由负责组织配置和控制的域加入计算机而言，这种情况通常都是一个需要解决的问题。 在 Windows 10 上，请按照下列步骤操作，检查 Device Guard  是否在运行：

1. 在 Windows 搜索框中输入“系统信息”，然后在搜索结果中选择“系统信息”   。

2. 在“系统摘要”  中，检查是否有“基于 Device Guard 虚拟化的安全性”  ；若有，检查是否处于“正在运行”  状态：

   [![Device Guard 存在且正在运行](troubleshooting-images/win/04-device-guard-sml.png)](troubleshooting-images/win/04-device-guard.png#lightbox)

如果已启用 Device Guard，请按照下列步骤操作，禁用 Device Guard：

1. 确保已按照上一部分所述禁用“Hyper-V”  （在“打开或关闭 Windows 功能”  下）。

2. 在 Windows 搜索框中，输入“gpedit.msc”  ，然后选择“编辑组策略”  搜索结果。 这些步骤将启动“本地组策略编辑器”  。

3. 在“本地组策略编辑器”  中，依次转到“计算机配置”>“管理模板”>“系统”>“Device Guard”  ：

   [![“本地组策略编辑器”中的“Device Guard”](troubleshooting-images/win/05-group-policy-editor-sml.png)](troubleshooting-images/win/05-group-policy-editor.png#lightbox)

4. 将“打开基于虚拟化的安全性”  更改为“已禁用”  （如上所示），再退出“本地组策略编辑器”  。

5. 在 Windows 搜索框中，输入“cmd”  。 右键单击搜索结果中的“命令提示符”   ，再选择“以管理员身份运行”  。

6. 将以下命令复制并粘贴到命令提示符窗口（如果正在使用驱动器 Z:  ，请改为选择未使用的驱动器号）：

    ```cmd
    mountvol Z: /s
    copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
    bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
    bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
    mountvol Z: /d
    ```

7. 重新启动计算机。 在启动屏幕上，应该会出现类似以下消息的提示：

   **是否要禁用 Credential Guard?**

   按下指示的键，以根据提示禁用 Credential Guard。

8. 重启计算机后，再次检查，以确保 Hyper-V 已禁用（如前述步骤所述）。

如果 Hyper-V 仍未禁用，域加入计算机的策略可能会阻止禁用 Device Guard 或 Credential Guard。 在这种情况下，可以向域管理员申请豁免，以便能够选择禁用 Credential Guard。 或者，如果必须使用 HAXM，则可以使用未加入域的计算机。

## <a name="additional-troubleshooting-tips"></a>其他故障排除提示

以下建议通常有助于诊断 Android Emulator 问题。

### <a name="starting-the-emulator-from-the-command-line"></a>从命令行启动仿真器

如果仿真器尚未运行，则可以从命令行（而不是从 Visual Studio 中）启动它以查看其输出。 通常，Android Emulator AVD 图像存储在以下位置（将“用户名”替换为 Windows 用户名）  ：

C:\\Users\\username\\.android\\avd  

可通过传入 AVD 的文件夹名称从此位置启动带有 AVD 图像的仿真器。 例如，此命令将启动名为“Pixel_API_27”的 AVD  ：

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator.exe" -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_API_27 -prop monodroid.avdname=Pixel_API_27
```

此示例假定将 Android SDK 安装在默认位置 C:\\Program Files (x86)\\Android\\android-sdk；如果不是，请在计算机上修改上述 Android SDK 所在的路径  。

运行此命令时，它将在仿真器启动时生成许多行输出。 具体而言，如果硬件加速已启用并且正常运行（在此示例中，使用 HAXM 进行硬件加速），将出现如下所示的行：

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: HAXM version 6.2.1 (4) is installed and usable.
```

### <a name="viewing-device-manager-logs"></a>查看设备管理器日志

通常可通过查看设备管理器日志来诊断仿真器问题。 这些日志将写入以下位置：

C:\\Users\\username\\AppData\\Roaming\\XamarinDeviceManager 

可使用文本编辑器（如记事本）查看每个 DeviceManager.log 文件  。 以下示例日志项目表示，在计算机上未找到 HAXM：

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
::: zone pivot="macos"

## <a name="deployment-issues-on-macos"></a>在 macOS 上部署时出现的问题

部署应用时，仿真器可能会显示一些错误消息。 下面介绍最常见的错误和解决方案。

### <a name="deployment-errors"></a>部署错误

若出现有关无法在仿真器上安装 APK 或无法运行 Android Debug Bridge (adb) 的错误消息，请验证 Android SDK 能否连接到仿真器  。 要验证连接情况，请使用以下步骤：

1. 通过“Android Device Manager”启动仿真器（选择虚拟设备并单击“启动”）   。

2. 打开命令提示符，转到 adb 的安装文件夹  。 如果将 Android SDK 安装在其默认位置，则“adb”位于“~/Library/Developer/Xamarin/android-sdk-macosx/platform-tools/adb”；如果不是，请在计算机上修改 Android SDK 所在的路径   。

3. 键入以下命令：

   ```shell
   adb devices
   ```

4. 如果可以通过 Android SDK 访问仿真器，那么仿真器应该就显示在附加设备列表中。 例如：

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. 如果仿真器不在此列表中，请启动“Android SDK 管理器”  ，应用所有更新，再尝试重启仿真器。

### <a name="mmio-access-error"></a>MMIO 访问错误

若显示“发生 MMIO 访问错误”，请重启仿真器  。

<a name="gps-mac" />

## <a name="missing-google-play-services"></a>缺少 Google Play Services

如果在仿真器中运行的虚拟设备未安装 Google Play Services 或 Google Play 商店，则在未安装这些软件包时创建虚拟设备通常会出现此情况。 创建虚拟设备时（请参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)），请务必选择以下一项或两项：

- Google API  &ndash; 在虚拟设备中包含 Google Play 服务。
- Google Play 商店  &ndash; 在虚拟设备中包含 Google Play 商店。

例如，此虚拟设备将包含 Google Play Services 和 Google Play 商店：

[![启用了 Google Play Services 和 Google Play 商店的示例 AVD](troubleshooting-images/mac/01-google-play-services-m75-sml.png)](troubleshooting-images/mac/01-google-play-services-m75.png#lightbox)

> [!NOTE]
> Google Play 商店图片仅适用于某些基本设备类型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

<a name="perf-mac" />

## <a name="performance-issues"></a>性能问题

性能问题通常由以下某个问题引起：

- 仿真器在没有硬件加速的情况下运行。

- 在仿真器中运行的虚拟设备未使用基于 x86 的系统映像。

以下部分更详细地介绍了这些方案。

### <a name="hardware-acceleration-is-not-enabled"></a>未启用硬件加速

如果未启用硬件加速，则在将应用部署到 Android Emulator 时，可能会弹出一个对话框，其中显示“设备将不加速运行”  。 如果不确定计算机上是否启用了硬件加速（或想知道哪种技术可以提供加速），请参阅下面的[硬件加速问题](#accel-issues-mac)，了解验证和启用硬件加速的步骤。

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>加速已启用但仿真器运行速度过慢 

导致此问题的常见原因是虚拟设备中未使用基于 x86 的映像。 创建虚拟设备时（请参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)），请确保选择基于 x86 的系统映像：

[![为虚拟设备选择 x86 系统映像](troubleshooting-images/mac/02-x86-virtual-device-m75-sml.png)](troubleshooting-images/mac/02-x86-virtual-device-m75.png#lightbox)

<a name="accel-issues-mac" />

## <a name="hardware-acceleration-issues"></a>硬件加速问题

无论使用虚拟机监控程序框架还是 HAXM 进行仿真器的硬件加速，都可能遇到因安装不当或 macOS 版本过期引起的问题。 以下部分可帮助你解决此问题。

<a name="hypervisor-issues" />

### <a name="hypervisor-framework-issues"></a>虚拟机监控程序框架问题

如果在新版 Mac 上使用 macOS 10.10 或更高版本，则 Android Emulator 将自动使用虚拟机监控程序框架进行硬件加速。 但是，某些旧版 Mac 或运行早于 10.10 版的 macOS 的 Mac 可能无法提供虚拟机监控程序框架支持。

要确定 Mac 是否支持虚拟机监控程序框架，请打开终端并输入以下命令：

```bash
sysctl kern.hv_support
```

若 Mac 支持虚拟机监控程序框架，则上述命令将返回以下结果：

```bash
kern.hv_support: 1
```

若 Mac 上没有虚拟机监控程序框架，则可以按照[使用 HAXM 加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#haxm-mac)中的步骤来使用 HAXM 进行加速。

### <a name="haxm-issues"></a>HAXM 问题

如果 Android Emulator 无法正常启动，这通常是 HAXM 问题所致。 导致 HAXM 问题的原因通常包括：与其他虚拟化技术冲突、设置不正确或 HAXM 驱动器不是最新版本。 按照[安装 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#install-haxm-mac) 中介绍的步骤，尝试重新安装 HAXM 驱动程序。

## <a name="additional-troubleshooting-tips"></a>其他故障排除提示

以下建议通常有助于诊断 Android Emulator 问题。

### <a name="starting-the-emulator-from-the-command-line"></a>从命令行启动仿真器

如果仿真器尚未运行，则可以从命令行（而不是从 Visual Studio for Mac 中）启动它以查看其输出。 通常，Android Emulator AVD 图像存储在以下位置：

~/.android/avd 

可通过传入 AVD 的文件夹名称从此位置启动带有 AVD 图像的仿真器。 例如，此命令将启动名为“Pixel_2_API_28”的 AVD  ：

```cmd
~/Library/Developer/Xamarin/android-sdk-macosx/emulator/emulator -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_2_API_28 -prop monodroid.avdname=Pixel_2_API_28
```

如果将 Android SDK 安装在其默认位置，则仿真器位于“~/Library/Developer/Xamarin/android-sdk-macosx/emulator”目录；如果不是，请在 Mac 上修改 Android SDK 所在的路径  。

运行此命令时，它将在仿真器启动时生成许多行输出。 具体而言，如果硬件加速已启用并且正常运行（在此示例中，使用虚拟机监控程序框架进行硬件加速），将出现如下示例的行：

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: Hypervisor.Framework OS X Version 10.13
```

### <a name="viewing-device-manager-logs"></a>查看设备管理器日志

通常可通过查看设备管理器日志来诊断仿真器问题。 这些日志将写入以下位置：

~/Library/Logs/XamarinDeviceManager 

可查看每个“Android Devices.log”文件（通过双击文件在控制台应用中将其打开）  。 以下示例日志项目指示未找到 HAXM：

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
