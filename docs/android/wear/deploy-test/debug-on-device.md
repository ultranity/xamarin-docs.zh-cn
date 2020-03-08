---
title: 在穿戴设备上调试
description: 本文介绍如何在磨损设备上调试 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 965ed4e802c05f8450192c0fec17fe31e464c779
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916657"
---
# <a name="debug-on-a-wear-device"></a>在穿戴设备上调试

_本文介绍如何在磨损设备上调试 Xamarin 应用程序。_

## <a name="overview"></a>概述

如果有 Android 磨损设备，如 Android 磨损 Smartwatch，则可以在设备上运行应用，而不是使用模拟器。 （如果你尚不熟悉部署和运行 Android 应用程序的过程，请参阅[Hello，磨损](~/android/wear/get-started/hello-wear.md)。）

## <a name="prepare-the-wear-device"></a>准备磨损设备：

使用以下步骤在 Android 磨损设备上启用调试：

1. 打开 Android 磨损设备上的 "**设置**" 菜单。

2. 滚动到菜单底部，点击 "**关于**"。

3. 点击内部版本号7次。

4. 在 "**设置**" 菜单上，点击 "**开发人员选项**"。

5. 确认已启用**ADB 调试**。

## <a name="debugging-over-usb"></a>通过 USB 调试

如果磨损设备有 USB 端口，则可以将磨损设备连接到计算机，将其部署到计算机，并像使用 Android 手机一样运行/调试应用程序（有关详细信息，请参阅[在设备上调试](~/android/deploy-test/debugging/debug-on-device.md)）。

## <a name="debugging-over-bluetooth"></a>通过蓝牙进行调试

如果磨损设备没有 USB 端口，则可以通过将应用的调试输出路由到连接到计算机的 Android 手机，将应用部署到基于蓝牙的设备。 

### <a name="prepare-your-phone"></a>准备手机

使用以下步骤来准备你的手机，以便与磨损设备建立蓝牙连接： 

1. 如果尚未这样做，请为 Xamarin Android 开发设置电话，如[设置用于开发的设备](~/android/get-started/installation/set-up-device-for-development.md)中所述。

2. 从 Google Play 商店下载并安装免费[Android](https://play.google.com/store/apps/details?id=com.google.android.wearable.app)应用。

### <a name="connect-the-device"></a>连接设备

使用以下步骤将你的磨损设备连接到手机：

1. 在将充当蓝牙中介的手机上（如上所述），启动 Android 应用。 

2. 点击 "**设置**" 图标。

3. 启用**通过蓝牙进行调试**。 你应会在 Android 应用程序的屏幕上看到以下状态：

    ```
    Host: disconnected
    Target: connected
    ```

4. 通过 USB 将手机连接到计算机。 在计算机上输入以下命令：

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    如果端口4444不可用，则可以使用具有访问权限的任何其他可用端口。 

    > [!NOTE]
    > 如果重启 Visual Studio 或 Visual Studio for Mac，则必须再次运行这些命令，以便设置与磨损设备的连接。

5. 当磨损设备提示你时，请确认你允许**ADB 调试**。 在 Android 应用程序中，应会看到状态更改为：

    ```
    Host: connected
    Target: connected
    ```

6. 完成上述步骤后，运行 `adb devices` 将显示 "电话" 和 "Android" 磨损设备的状态：

    ```
    List of devices attached
    127.0.0.1:4444    device
    019ad61df0a69399  device
    ```

此时，你可以将应用部署到磨损设备。

<a name="screenshots" />

### <a name="taking-screenshots"></a>拍摄屏幕截图

可以通过输入以下命令来获取磨损设备的屏幕截图： 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

输入以下命令，将屏幕快照复制到计算机：

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

输入以下命令，删除设备上的屏幕截图：

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```

### <a name="uninstalling-an-app"></a>卸载应用

可以通过输入以下命令从磨损设备卸载应用：

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

例如，若要删除包名称 `com.xamarin.weartest`的应用，请输入以下命令：

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

有关通过蓝牙调试 Android 磨损设备的详细信息，请参阅[通过蓝牙进行调试](https://developer.android.com/training/wearables/apps/bt-debugging.html)。

## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>使用配套手机应用调试应用程序

Android 应用与随附的 Android 手机应用一起打包在 Google Play 上（有关详细信息，请参阅使用[打包](~/android/wear/deploy-test/packaging.md)）。 不过，你仍然可以单独开发应用程序及其附属应用。 通过 Google Play 商店发布应用程序时，会将该应用程序与配套应用一起打包，并在可能的情况下自动安装。

使用配套应用调试应用程序： 

1. 生成配套应用并将其部署到手机。

2. 右键单击该 "磨损" 项目，并将其设置为 "默认启动项目"。

3. 将该磨损项目部署到可穿戴设备。

4. 在设备上运行和调试应用程序。

## <a name="summary"></a>总结

本文介绍了如何通过蓝牙为 Visual Studio 的应用配置应用程序的损耗调试，以及如何使用伴随电话应用程序调试应用程序。 它还提供了通过蓝牙调试应用程序的常见调试提示。
