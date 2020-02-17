---
title: Xamarin 热重启
description: 本文档介绍如何设置和使用 Xamarin 热重启来调试 iOS 应用。
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: jimmgarrido
ms.author: jigarrid
ms.date: 01/14/2020
ms.openlocfilehash: 2cf925a96e952e6b760da9ca5416e124a3e3716b
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77071148"
---
# <a name="xamarin-hot-restart-preview"></a>Xamarin 热重启（预览版）

![预览版功能](~/media/shared/preview.png)

Xamarin 热重启使你可以在开发过程中快速测试对应用进行的更改，包括多文件代码编辑、资源和引用。 它将新更改推送到调试目标上的现有应用捆绑包，这会使生成和部署周期快得多。

> [!NOTE]
> Xamarin 热重启当前在 Visual Studio 2019 版本 16.5 预览版中提供，并支持使用 Xamarin.Forms 的 iOS 应用。 对 Visual Studio for Mac 和非 Xamarin.Forms 应用的支持处于路线图上。

## <a name="requirements"></a>要求

- Visual Studio 2019 版本 16.5 预览版 2 或更高版本
- iTunes（64 位）
- Apple 开发人员帐户


## <a name="initial-setup"></a>初始设置

1. 确保将 iOS 项目设置为启动项目，并将生成配置设置为“调试|iPhone”。

   1. 如果这是现有项目，请转到“生成”>“配置管理器...” 并确保为 iOS 项目启用“部署”。

2. 在工具栏中选择并单击“本地设备”，以启动设置向导：

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. 如果未安装 iTunes，请单击“下载 iTunes”以下载安装程序。 iTunes 安装完成时单击“下一步”。

4. 将 iOS 设备连接到计算机。 检测到后，设备名称会显示在向导中。 单击 **“下一步”**。

5. 输入 Apple 开发人员帐户凭据，然后单击“下一步”。

6. 使用下拉菜单选择开发团队，以便在项目中启用[自动预配](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)。 单击 **“完成”**。

> [!NOTE]
> 建议使用自动预配，以便可以轻松地为部署配置其他 iOS 设备。 但是，如果存在正确的预配配置文件，则可以禁用它并继续使用手动预配。

## <a name="use-xamarin-hot-restart"></a>使用 Xamarin 热重启
初始设置之后，连接的设备会出现在调试目标下拉菜单中。 若要调试应用，请在下拉菜单中选择你的设备，然后单击“运行”按钮。 你可能会在 Visual Studio 中看到一条消息，指示你在设备上手动启动应用以启动调试会话。

可以在调试时编辑代码文件，然后按调试工具栏中的“重启”按钮，或使用 Ctrl+Shift+F5 重启调试会话，并应用新更改：

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

## <a name="limitations"></a>限制
- 当前仅支持使用 Xamarin.Forms 和 iOS 设备生成的 iOS 应用。
- 不支持情节提要和 XIB 文件，如果应用尝试在运行时加载这些文件，则应用可能会崩溃。 如果在应用中使用这些文件，请告知我们，因为我们希望在将来支持此方案。
- 静态 iOS 库和框架不受支持。 如果应用尝试加载这些内容，则可能会看到运行时错误或崩溃。 但是，动态 iOS 库受支持。
- 无法使用 Xamarin 热重启创建应用捆绑包进行发布。 仍需要 Mac 计算机进行完整编译、签名和部署，以便将应用程序投入生产。

## <a name="troubleshoot"></a>疑难解答
- 如果 iTunes 是通过 Microsoft Store 安装，则设置向导不会检测到 iTunes。 首先需要卸载该版本，然后[从 Apple下载安装程序](https://go.microsoft.com/fwlink/?linkid=2101014)。
- 存在一个已知问题，即启用设备特定的生成会阻止应用进入调试模式。 解决方法是在“属性”>“iOS 版本”下禁用此项，然后重新尝试调试。 此问题将在未来版本中得到解决。
- 如果设备上已存在该应用，尝试使用热重启进行部署可能会失败，并出现 `AMDeviceStartHouseArrestService` 错误。 解决方法是在设备上卸载该应用，然后再次部署。

若要报告其他问题，请在[“帮助”>“发送反馈”>“报告问题”](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem)处使用反馈工具。
