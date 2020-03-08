---
title: Xamarin.iOS 中的 Apple Pay 功能
description: 向应用程序添加功能通常需要其他预配设置。 本指南将介绍 Apple Pay 功能所需的设置。
ms.prod: xamarin
ms.assetid: 735CC916-16A4-471B-87F7-0535E24288D7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 54fcc3bb683f6ca4515d807da5c3345cf71a2df3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917738"
---
# <a name="apple-pay-capabilities-in-xamarinios"></a>Xamarin.iOS 中的 Apple Pay 功能

_向应用程序添加功能通常需要其他设置。本指南介绍 Apple Pay 功能所需的设置。_

通过 Apple Pay，用户可以使用 iOS 设备支付实体商品。 本部分介绍如何在 Apple 开发人员中心创建 Apple Pay 所需的所有组件。

通过开发人员中心预配新应用时需采取三个步骤：

1. 创建商户 ID。
2. 创建具有 Apply Pay 功能的应用 ID 并向其添加商户。
3. 生成商户 ID 的证书。

下述步骤将引导你完成创建上述各项：

<a name="merchantid" />

## <a name="create-merchant-id"></a>创建商户 ID

商户 ID 用于使 Apple Pay 知道你可接受付款，并会传递到 PassKit 的 `PaymentRequest` 方法以及用于 Apple Pay 权利中：

1. 浏览到 [Apple 开发人员中心](https://developer.apple.com/account/)，然后转到“证书、标识符和配置文件”部分：

    ![开发人员中心商户 ID 选择](apple-pay-capabilities-images/image57.png)

2. 在“标识符”下，选择“商户 ID”，然后选择“ **”+** 来创建新的商户 ID：  

3. 使用新的描述和标识符填写表单，如下所示。 此描述便于你识别此 ID，并可在以后进行更改。 此标识符对你必须唯一，且必须以字符串 `merchant` 开头。 Apple 推荐标识符采用如下格式：`merchant.com.[Your-App-Name]`：

    ![新的商户 ID 详细信息](apple-pay-capabilities-images/image58.png)

4. 确认详细信息，然后注册 ID： ****   

    ![商户 ID 确认](apple-pay-capabilities-images/image59.png)

<a name="appid" />

## <a name="create-an-app-id-with-the-apple-pay-capability-that-includes-the-merchant-id"></a>创建一个应用 ID，其中具有包含此商户 ID 的 Apple Pay 功能

1. 在[开发人员中心](https://developer.apple.com/account/)中的“标识符”下，单击“应用 ID”：

    ![在开发人员中心中选择应用 ID](apple-pay-capabilities-images/image6.png)

2. 选择“ **”+** 按钮来添加新的应用 ID：

    ![添加新的应用 ID 按钮](apple-pay-capabilities-images/image27.png)

3. 输入应用 ID 的名称，并赋予显式应用 ID：    

    ![应用 ID 详细信息屏幕](apple-pay-capabilities-images/image35.png)

4. 在“应用服务”下，选择 Apple Pay：    

    ![应用服务 Apple Pay](apple-pay-capabilities-images/image36.png)

5. 选择“继续”，然后选择“注册”。 请注意，在确认屏幕上，Apple Pay 会以黄色符号显示已选择“可配置”的内容：

    ![Apple Pay 确认屏幕](apple-pay-capabilities-images/image37.png)

6. 返回应用 ID 列表，并选择刚才创建的应用 ID：  

    ![编辑应用 ID](apple-pay-capabilities-images/image38.png)

7. 向下滚动到此展开部分的底部，然后单击“编辑”。
8. 在列表中向下滚动到 Apple Pay，然后单击“编辑”按钮：  

    ![编辑 Apple Pay 应用 ID 详细信息](apple-pay-capabilities-images/image39.png)

9. 选择要用于此应用 ID 的商户 ID，然后单击“继续”：  

    ![选择要用于应用 ID 的商户 ID](apple-pay-capabilities-images/image40.png)

10. 确认商户 ID 分配，然后按“分配”：  

    ![确认屏幕](apple-pay-capabilities-images/image41.png)

现在此应用 ID 可用于生成或再生成新的预配配置文件，正如[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南中所述。

<a name="certificate" />

## <a name="create-a-certificate-for-your-merchant-id"></a>创建商户 ID 的证书

Apple 需要一个证书来加密与交易相关联的敏感数据。 每个创建的商户 ID 必须具有自己的证书。

若要创建证书，请执行以下步骤：

1. 选择上面创建的商户 ID，然后按“编辑”：

    ![编辑商户 ID 对话框](apple-pay-capabilities-images/image42.png)

2. 在 iOS 商户 ID 设置屏幕上，单击“创建证书”：

    ![创建支付处理证书](apple-pay-capabilities-images/image43.png)

3. 回答以下问题：

    ![支付是否仅在中国处理](apple-pay-capabilities-images/image44.png)

4. 此时系统将提示创建一个证书签名请求：

    ![创建证书签名请求](apple-pay-capabilities-images/image45.png)

    > [!IMPORTANT]
    > 如果使用 Apple Pay 的支付提供商（例如 JudoPay 或 Stripe），则他们可提供格式正确的 CSR 供你目前使用。 有关请求的信息，请参阅 [Stripe](https://stripe.com/docs/apple-pay/apps#csr) 站点。 若要创建自己的 CSR，请按照下方步骤 5-8 操作。 创建 CSR 后请转到步骤 9。

5. 打开密钥链访问应用程序，然后浏览到“密钥链访问”>“证书助手”>“从证书机构请求证书:”

     ![在 Mac 上使用密钥链创建 CSR](apple-pay-capabilities-images/image46.png)

6. 输入电子邮件地址，输入私有密钥的名称，将 CA 电子邮件地址留空，选择“保存到磁盘”选项，然后选择“允许我指定密钥对信息”：

     ![证书信息对话框](apple-pay-capabilities-images/image47.png)

7. 将 CSR 保存到方便位置：

     ![将 CSR 保存到本地计算机](apple-pay-capabilities-images/image48.png)

8. 在密钥对信息屏幕上，将“密钥大小”设置为“256 位”，将“算法”设置为“ECC”，然后单击“继续”：

     ![输入密钥对信息对话框](apple-pay-capabilities-images/image49.png)

9. 在开发人员中心中，单击“继续”上传此 CSR：

     ![准备将 CSR 上传到开发人员中心](apple-pay-capabilities-images/image50.png)

10. 单击 **“选择文件...”** 选择 CSR 并按“继续”，将其上传到开发人员门户：

     ![将 CSR 上传到开发人员中心](apple-pay-capabilities-images/image51.png)

11. 证书生成后，下载并双击证书，将其安装到密钥链。

有关使用 Apple Pay 的详细信息，请参阅以下指南：

* [Apple Pay 简介](~/ios/platform/apple-pay.md)

## <a name="next-steps"></a>后续步骤

下表列出了可能需要采取的其他步骤：

* 在应用中使用框架命名空间。
* 将所需权利添加到应用。 有关所需权利以及添加方法的详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在应用的“iOS 捆绑包签名”中，确保“自定义权利”设置为“Entitlements.plist”。 ****  **** 这 _不_ "调试" 和 "iOS 模拟器" 生成的默认设置。

如果遇到与应用服务有关的问题，请参考主要指南的[故障排除](~/ios/deploy-test/provisioning/capabilities/index.md)部分。
