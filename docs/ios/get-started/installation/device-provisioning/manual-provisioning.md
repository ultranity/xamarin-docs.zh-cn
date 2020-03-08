---
title: Xamarin.iOS 的手动预配
description: Xamarin.iOS 成功安装后，iOS 开发的下一步是 iOS 设备预配。 本指南介绍如何使用手动预配设置开发证书和配置文件。
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/15/2017
ms.openlocfilehash: 945a42485486dbfddfd023a72e88d9127651c71f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911511"
---
# <a name="manual-provisioning-for-xamarinios"></a>Xamarin.iOS 的手动预配

Xamarin.iOS 成功安装后，iOS 开发的下一步是预配 iOS 设备。_本指南介绍如何使用手动预配设置开发证书和配置文件。_

> [!NOTE]
> 此页上的说明与付费访问 Apple 开发者计划的开发人员相关。 如果你有一个免费帐户，请参阅[免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南，详细了解基于设备的测试。

## <a name="creating-a-signing-identity"></a>创建签名标识

设置开发设备的第一步是创建签名标识。 签名标识包含以下两项：

- 开发证书
- 私钥

开发证书和关联[密钥](#understanding-certificate-key-pairs)对 iOS 开发者至关重要：它们生成了你的 Apple 标识，并将你与给定设备和配置文件相关联以用于开发，类似于将你的数字签名放入应用程序。 Apple 会检查证书以控制对你可以部署的设备的访问。

可通过访问 Apple Members Center 的[证书、标识符和配置文件](https://developer.apple.com/account/overview.action)（要求登录）部分管理开发团队、证书和配置文件。 Apple 要求你拥有签名标识才能为设备或模拟器生成代码。  

> [!IMPORTANT]
> 请务必注意，任何时候，一次只能有两个 iOS 开发证书。 如果需要创建更多证书，需要撤销现有证书。 使用已撤销证书的计算机不能对应用进行签名。

若要生成签名标识，请执行以下操作：

1. 登录[开发人员门户中的证书、标识符和描述文件部分](https://developer.apple.com/account/overview.action)，从“iOS 应用”  列选择“证书”  部分。 然后，按“+”  以创建新证书：

    [![](manual-provisioning-images/cert-plus.png "Click the + to create a new certificate")](manual-provisioning-images/cert-plus.png#lightbox)

2. 对于证书类型，选择“iOS 应用开发”  选项，然后单击“继续”  。 此屏幕可能会根据帐户特权有所不同：

    [![](manual-provisioning-images/cert-first.png "Select the iOS App Development option for the certificate type")](manual-provisioning-images/cert-first.png#lightbox)

3. 请求“证书签名请求”，上传该请求以手动生成证书。 若要执行此操作，在 Mac 上启动 **Keychain Access**。 导航到主菜单，选择“证书助手”  和“向证书颁发机构申请证书...”  ，如下所示：

      [![](manual-provisioning-images/key-first.png "Request a Certificate Signing Request")](manual-provisioning-images/key-first.png#lightbox)

4. 填写你的信息，并选择“保存到磁盘”  选项：

    [![](manual-provisioning-images/key-second.png "Fill in your information")](manual-provisioning-images/key-second.png#lightbox)

5. 在易于找到的位置保存 CSR：

    [![](manual-provisioning-images/cert-third.png "Save the CSR")](manual-provisioning-images/cert-third.png#lightbox)

6. 返回设置门户，将证书上传到门户并提交：

    [![](manual-provisioning-images/cert-second.png "Upload the Certificate to the portal")](manual-provisioning-images/cert-second.png#lightbox)

    如果没有管理员权限，证书必须由管理员或团队代理批准。

7. 证书经过批准后，从设置门户下载此证书：

    [![](manual-provisioning-images/status-dev.png "Download the Certificate from the Provisioning Portal")](manual-provisioning-images/status-dev.png#lightbox)

8. 双击下载的证书，启动 Keychain Access 并打开“我的证书”  面板，显示新证书和关联私钥：

    [![](manual-provisioning-images/keychain.png "The Certificate in Keychain Access")](manual-provisioning-images/keychain.png#lightbox)

### <a name="understanding-certificate-key-pairs"></a>了解证书密钥对

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

开发人员配置文件包含证书、关联密钥，以及任何与帐户关联的设置配置文件。 实际上有两个版本的开发者配置文件 — 一个在开发者门户中，另一个在本地 Mac 上。 两者之间的区别在于它们所包含的密钥类型：_门户上的配置文件包含与证书关联的所有公钥，而本地 Mac 上的副本包含所有私钥_。 要使证书生效，密钥对必须匹配。 在本地 Mac 上保存开发者配置文件的备份，因为如果密钥丢失，需要重新生成所有证书和设置配置文件。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

开发人员配置文件包含证书、关联密钥，以及任何与帐户关联的设置配置文件。 实际上有两个版本的开发者配置文件 — 一个在开发者门户中，另一个在 Mac 上。 两者之间的区别在于它们所包含的密钥类型：_门户上的配置文件包含与证书关联的所有公钥，而 Mac 上的副本包含所有私钥_。 要使证书生效，密钥对必须匹配。 在 Xamarin 生成主机的 Mac 上保存开发人员配置文件的备份，因为如果密钥丢失，需要重新生成所有证书和设置配置文件。

-----

> [!WARNING]
> 丢失证书和关联密的钥会很麻烦，因为需要撤销现有证书并重新设置任何关联的设备，包括为临时部署注册的设备。 成功设置开发证书后，导出备份副本并将其存储在安全位置。 有关如何执行此操作的详细信息，请参阅 Apple 文档中[维护证书](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html)指南的“导出与导入证书和配置文件”部分。

<a name="provisioning" />

## <a name="provisioning-an-ios-device-for-development"></a>预配 iOS 设备进行开发

现在你已经生成 Apple 标识，并且已具有开发证书，现在必须设置预配配置文件和所需实体，以便将应用部署到 Apple 设备。 设备必须运行 Xcode 支持的 iOS 版本 - 可能需要更新设备和/或 Xcode。

<a name="adddevice" />

## <a name="add-a-device"></a>添加设备

创建用于开发的设置配置文件时，必须声明哪些设备可以运行应用程序。 为了实现此目的，开发者门户每个日历年可以添加最多 100 台设备，还可以从这里选择要添加到特定设置配置文件的设备。 请按照以下步骤在 Mac 上将设备添加到开发人员门户

1. 启动 Xcode。
2. 使用提供的 USB 电缆连接要设置到 Mac 的设备。
3. 从“Windows”  菜单中选择“设备”  ：

   [![](manual-provisioning-images/add01.png "From the Windows menu select Devices")](manual-provisioning-images/add01.png#lightbox)

4. 从“设备”窗口左侧的“设备”  列表选择所需的 iOS 设备。
5. 突出显示“标识符”  字符串并将其复制到剪贴板：

   [![](manual-provisioning-images/add02.png "Highlight the Identifier string")](manual-provisioning-images/add02.png#lightbox)

6. 在 Safari 中，导航到 [Apple 开发者中心](https://developer.apple.com/membercenter/index.action)并登录。
7. 单击“证书、标识符和描述文件”  链接：

   [![](manual-provisioning-images/add03.png "Click the Certificates, Identifiers  Profiles link")](manual-provisioning-images/add03.png#lightbox)

8. 单击“设备”  链接：

   [![](manual-provisioning-images/add04.png "Click on the Devices link")](manual-provisioning-images/add04.png#lightbox)

9. 单击“+”  按钮：

   [![](manual-provisioning-images/add05.png "Click the + button")](manual-provisioning-images/add05.png#lightbox)

10. 为新设备提供名称并将前面复制的设备“标识符”  粘贴到“UUID”  字段：

    [![](manual-provisioning-images/add06.png "Provide a name for the new device and the device Identifier")](manual-provisioning-images/add06.png#lightbox)

11. 单击“继续”  按钮。
12. 最后，检查信息并单击“注册”  按钮：

    [![](manual-provisioning-images/add07.png "Review the information")](manual-provisioning-images/add07.png#lightbox)

对将用于测试或调试 Xamarin.iOS 应用程序的任何 iOS 设备重复上述步骤。

将设备添加到开发人员门户后，需要创建设置配置文件并向其添加设备。

<a name="provisioningprofile" />

## <a name="creating-a-development-provisioning-profile"></a>创建开发预配配置文件

与开发证书一样，预配配置文件可以通过 Apple Members Center 的[证书、标识符和配置文件](https://developer.apple.com/account/overview.action)部分手动创建。

创建预配配置文件以前，应先创建应用 ID  。 应用 ID 是反向 DNS 样式字符串，用于唯一标识应用程序。 以下步骤演示如何创建通配符应用 ID  ，以用于生成和安装大多数应用程序。 “显式应用 ID”  只允许安装一个应用程序（具有匹配的捆绑 ID），且通常用于某些 iOS 功能，如 Apple Pay 和 HealthKit。 有关创建“显式应用 ID”的信息，请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

### <a name="app-id"></a>应用 ID

1. 在[开发者门户](https://developer.apple.com/account/overview.action)中找到Apple 开发者中心的*证书、标识符和描述文件*部分。 在“标识符”  下选择“应用 ID”  。
2. 单击“+”  按钮，并提供“名称”  ：

    [![](manual-provisioning-images/appid05a.png "Provide a Name")](manual-provisioning-images/appid05a.png#lightbox)
3. 应预设应用前缀。 为应用后缀选择“通配符应用 ID”  。 以 `com.[DomainName].*` 格式输入捆绑 ID：

   [![](manual-provisioning-images/appid05b.png "Enter a Bundle ID")](manual-provisioning-images/appid05b.png#lightbox)

4. 单击“继续”  按钮，按照屏幕上的说明创建新的应用程序 ID。

### <a name="provisioning-profile"></a>预配配置文件

创建好应用 ID 后，即可生成配置文件。 此配置文件包含以下信息：与此配置文件相关的应用有哪个（些）（如果是通配符应用 ID，则为多个应用）  ，谁  可以使用配置文件（具体取决于添加了哪些开发人员证书），以及哪些  设备可以安装应用。

若要手动创建用于开发的配置文件，请执行以下操作：

1. 使用 Safari 浏览到 [Apple 开发者成员中心](https://developer.apple.com/membercenter/index.action)，然后在“证书、标识符和描述文件”  部分下选择配置文件。
2. 在右上角单击“+”  按钮，以创建新的配置文件。
3. 在“开发”  部分中，选中“iOS 应用开发”  旁边的单选按钮，再按“继续”  ：

    [![](manual-provisioning-images/provisioning-profile01.png "Select the type of profile to create")](manual-provisioning-images/provisioning-profile01.png#lightbox)
4. 在下拉菜单中，选择要使用的应用 ID：

    [![](manual-provisioning-images/provisioning-profile02.png "Select the App ID that to use")](manual-provisioning-images/provisioning-profile02.png#lightbox)
5. 选择要在预配配置文件中添加的一个或多个证书，再按“继续”  ：

    [![](manual-provisioning-images/provisioning-profile03.png "Select the Certificates to include in the provisioning profile")](manual-provisioning-images/provisioning-profile03.png#lightbox)
6. 选择将安装应用的所有设备。

    [![](manual-provisioning-images/provisioning-profile04.png "Select all the devices that the app will be installed on")](manual-provisioning-images/provisioning-profile04.png#lightbox)
7. 为预配配置文件提供可识别名称，再按“继续”  创建配置文件：

    [![](manual-provisioning-images/provisioning-profile05.png "Provide the Provisioning Profile with an identifiable a name")](manual-provisioning-images/provisioning-profile05.png#lightbox)
8. 按“下载”  ，将预配配置文件下载到 Mac 中：

    [![](manual-provisioning-images/provisioning-profile06.png "Download the provisioning profile")](manual-provisioning-images/provisioning-profile06.png#lightbox)

9. 双击文件，在 Xcode 中安装配置文件。 请注意，除非打开，否则 Xcode 可能不会显示任何可见提示来说明它已安装配置文件。 这可以通过浏览到“Xcode”>“窗口”>“设备和模拟器”  进行验证。 右键单击你的设备，然后选择“显示预配配置文件”  ...

      [![](manual-provisioning-images/provisioning-profile07-sml.png "Viewing the profile in Xcode")](manual-provisioning-images/provisioning-profile07.png#lightbox)

成功创建配置文件后，可能需要刷新 Xcode，以便所有开发证书都可用于 Visual Studio for Mac 和 Visual Studio。

<a name="download" />

## <a name="downloading-profiles-and-certificates-in-xcode"></a>在 Xcode 中下载配置文件和证书

Apple Developer 门户中已创建的证书和配置文件可能不会自动显示在 Xcode 中。 因此，可能需要下载它们，以便其可由 Visual Studio for Mac 和 Visual Studio 访问。 若要更新和下载 Apple Developer 门户中创建的任何证书，请执行以下操作：

1. 退出 Visual Studio for Mac 或 Visual Studio。
2. 启动 Xcode。
3. 选择“Xcode 菜单”>“偏好设置...” 
4. 单击“帐户”  选项卡。
5. 选择团队，然后单击“下载手动配置文件”按钮  ：

    [![下载手动配置文件](manual-provisioning-images/selectteam1.png)](manual-provisioning-images/selectteam1.png#lightbox)

6. 退出 Xcode。
7. 启动 Visual Studio for Mac 或 Visual Studio。

Visual Studio for Mac 或 Visual Studio 将有新的证书或预配配置文件可供使用。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

> [!IMPORTANT]
> 可能需要停止和重启 Visual Studio for Mac，才能看到任何新的或修改的证书或由 Xcode 更新的配置文件。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> 可能需要停止和重启 Visual Studio，才能看到任何新的或修改的证书或由 Xcode 更新的配置文件。

-----

## <a name="provisioning-for-application-services"></a>对应用程序服务进行预配

Apple 提供一系列精选的特殊应用服务（亦称为“功能”），可以为 Xamarin.iOS 应用激活这些服务。 必须同时在 iOS 设置门户（创建“应用 ID”  时）和“Entitlements.plist”  文件（它是 Xamarin.iOS 应用程序项目的一部分）上配置这些应用程序服务。 若要了解如何将应用程序服务添加到应用，请参阅[功能简介](~/ios/deploy-test/provisioning/capabilities/index.md)指南和[使用授权](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 创建具备所需应用服务的应用 ID。
- 创建含有此应用 ID 的新[配置文件](#provisioningprofile)。
- 在 Xamarin.iOS 项目中设置权利

## <a name="deploying-to-a-device"></a>部署到设备

这时，设置应已完成，且应用已准备好部署到设备。 为此，请执行以下步骤：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

> [!IMPORTANT]
> 开始之前，请务必先选择 Info.plist  中的“手动设置”  。

1. 在 Mac 上插入设备。
2. 在项目的“Info.plist”  中，确保捆绑标识符与应用 ID 匹配（除非应用 ID 是通配符）：

   ![输入标识符](manual-provisioning-images/deploydevice01xs.png)

3. 右键单击项目，查看“项目选项”对话框，并浏览到“生成”>“iOS 捆绑签名”  。 从“签名标识”  和“预配配置文件”  旁边的下拉列表中，验证 Visual Studio for Mac 是否可以显示正确的配置文件，然后选择特定的标识和配置文件：

   ![选择特定的标识和配置文件](manual-provisioning-images/deploydevice02xs.png)

   如果设置为“自动”  ，Visual Studio for Mac 将基于步骤 2 中设置的捆绑 ID 选择标识和配置文件。

4. 请确保将生成配置设置为 **iPhone** / **iPad**，而不是模拟器。
5. 在 Visual Studio for Mac 中，单击“运行”  并查看在设备上运行的应用。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> 开始之前，请务必先选择“项目”>“预配属性...”中的“手动预配”   。

1. 将设备插入 Mac 生成主机。
2. 在项目的“Info.plist”  中，确保捆绑标识符与应用 ID 匹配：

   ![输入标识符](manual-provisioning-images/servicevs01.png)

3. 右键单击项目，查看“项目选项”对话框，并浏览到“生成”>“iOS 捆绑签名”  。 从“签名标识”  和“预配配置文件”  旁边的下拉列表中，验证 Visual Studio 是否可以显示正确的配置文件，然后选择特定的标识和配置文件。

    如果设置为“自动”  ，Visual Studio 将基于步骤 2 中设置的捆绑 ID 选择标识和配置文件。

4. 请确保将生成配置设置为 **iPhone** 或 **iPad**，而不是模拟器。
5. 在 Visual Studio 中单击“运行”  并查看在设备上运行的应用。

-----

## <a name="summary"></a>总结

本指南介绍了针对 Xamarin.iOS 设置开发环境所需的步骤。 探讨了如何使用关于开发人员、开发团队、可运行应用的设备和单个应用 ID 的信息对应用程序进行代码签名。

## <a name="related-links"></a>相关链接

- [可用设置](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [应用分发](~/ios/deploy-test/app-distribution/index.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
- [Apple - 应用分发指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
