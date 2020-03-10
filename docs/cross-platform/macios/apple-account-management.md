---
title: Apple 帐户管理
description: 本文档介绍如何在 Visual Studio for Mac 和 Visual Studio 2019 中使用 Apple 帐户管理功能。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 03/05/2020
ms.openlocfilehash: 17607e09a141fd29cd81cde93d812b20e62a9af8
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946245"
---
# <a name="apple-account-management"></a>Apple 帐户管理

Visual Studio 中的 Apple 帐户管理接口为查看与 Apple ID 相关联的开发团队提供信息提供了一种方法。 它允许你执行以下操作：

- **添加 Apple 开发人员帐户**
- **查看签名证书和预配配置文件**
- **创建新的签名证书**
- **下载现有的预配配置文件**

> [!IMPORTANT]
> Xamarin 的 Apple 帐户管理工具仅显示有关付费 Apple 开发人员帐户的信息。 若要了解如何在没有付费 Apple 开发人员帐户的设备上测试应用，请参阅[适用于 Xamarin 应用的免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

## <a name="requirements"></a>要求

Apple 帐户管理可在 Visual Studio for Mac、Visual Studio 2019 和 Visual Studio 2017 （版本15.7 及更高版本）上找到。 你还必须拥有付费的 Apple 开发人员帐户才能使用此功能。 有关 Apple 开发人员帐户的详细信息，请访问[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

> [!NOTE]
> 在开始之前，请确保先接受[Apple 开发人员门户](https://developer.apple.com/account/)中的任何用户许可协议。

## <a name="add-an-apple-developer-account"></a>添加 Apple 开发人员帐户

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 请参阅**Apple 开发人员帐户 > 的 Visual Studio > 首选项**，然后单击 " **+** " 按钮以打开 "登录" 对话框：

    ![Visual Studio for Mac 首选项中的 "Apple 开发人员帐户" 页的 AScreenshot。](apple-account-management-images/add-account-vsm.png)

2. 输入你的 Apple ID 和密码，然后单击 "**登录**"。 这会将你的凭据保存在此计算机上的 secure 密钥链中。

3. 在 "警报" 对话框中选择 "**始终允许**"，以允许 Visual Studio 使用您的凭据：

    ![始终允许警报对话框](apple-account-management-images/image4.png)

4. 成功添加帐户后，你将看到你的 Apple id 和你的 Apple ID 所属的任何团队：

    ![添加了帐户的 Apple 开发人员帐户对话框](apple-account-management-images/image5.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> 如果使用的是 Visual Studio 2017 或 Visual Studio 2019 （版本16.4 及更早版本），则在继续操作之前，需要将[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 请参阅 "**工具" > "Xamarin > Apple 帐户" > 选项**，然后单击 "**添加**"：

    ![Visual Studio 选项中 Apple 帐户页的屏幕截图。](apple-account-management-images/add-account-vsw.png)

2. 输入你的 Apple ID 和密码，然后单击 "**登录**"。

3. 成功添加帐户后，你将看到你的 Apple id 和你的 Apple ID 所属的任何团队：

    ![添加了帐户的开发人员帐户页的屏幕截图。](apple-account-management-images/accounts-vsw.png)

-----

## <a name="view-signing-certificates-and-provisioning-profiles"></a>查看签名证书和预配配置文件

选择一个团队，然后单击 "**查看详细信息 ...** " 打开一个对话框，其中显示了计算机上安装的签名标识和预配配置文件的列表。

"团队详细信息" 对话框显示按类型组织的签名标识列表。 "**状态**" 列建议你证书是否为： 

- **有效**–签名标识（证书和私钥）已安装在计算机上，并且未过期。

- **不在密钥链中**-Apple 的服务器上有有效的签名标识。 若要在计算机上安装此程序，必须从另一台计算机中导出它。 无法从 Apple 开发人员门户下载签名标识，因为它不包含私钥。

- **缺少私钥**-在密钥链中安装了不带私钥的证书。

- 已**过期**–证书已过期。 你应将其从密钥链中删除。

  ![团队详细信息对话框信息](apple-account-management-images/image7.png)

## <a name="create-a-signing-certificate"></a>创建签名证书

若要创建新的签名标识，请单击 "**创建证书**" 打开下拉菜单，然后选择要创建的[证书类型](https://help.apple.com/xcode/mac/current/#/dev80c6204ec)。 如果您具有正确的权限，则几秒钟后将显示一个新的签名标识。

如果下拉中的某个选项灰显并未选择，则表示你没有正确的团队权限来创建此类证书。

## <a name="download-provisioning-profiles"></a>下载预配配置文件

"团队详细信息" 对话框还显示连接到开发人员帐户的所有配置文件的列表。 可以通过单击 "**下载所有配置文件**" 将所有预配配置文件下载到本地计算机。


## <a name="troubleshoot"></a>故障排除

- 批准新的 Apple 开发人员帐户可能需要几个小时。 在批准帐户之前，你将无法启用自动设置。

- 如果添加 Apple 开发人员帐户失败，并 `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`消息，请确保你使用的 Apple ID 具有适用于 Apple 开发人员计划的有效付费成员资格。 若要使用付费的 Apple 开发人员帐户，请参阅[适用于 Xamarin apps 的免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

- 如果尝试创建新的签名证书失败，并出现错误 "`You have reached the limit for certificates of this type`"，则已生成允许的最大证书数。 若要解决此问题，请浏览到[Apple 开发人员中心](https://developer.apple.com/account/ios/certificate/distribution)，并撤消其中一个生产证书。

- 如果在 Visual Studio for Mac 上登录帐户时遇到问题，可能的解决方法是打开密钥链应用程序，然后在 "**类别**" 下选择 "**密码**"。 搜索 `deliver.` 并删除找到的所有条目。

## <a name="known-issues"></a>已知问题

- 默认情况下，分发预配配置文件将面向应用商店。 应手动创建内部配置文件或临时配置文件。
