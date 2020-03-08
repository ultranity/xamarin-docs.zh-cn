---
title: App Store 分发
description: 本文档介绍如何在 App Store 中分发 Xamarin.iOS 应用程序。 其中介绍了如何创建分发证书、如何创建分发预配配置文件以及如何配置 iTunes Connect 并提交应用。
ms.prod: xamarin
ms.assetid: B07E2C1F-A6DF-43CB-BFB0-0252A5558467
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/23/2017
ms.openlocfilehash: 73c4e992fae1a2d525670604d98f277fe4fde794
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78913584"
---
# <a name="app-store-distribution"></a>App Store 分发

Xamarin.iOS 应用开发完成后，软件开发生命周期中的下一步是使用 iTunes App Store 将应用分发给用户。 这是分发应用程序的最常见方法。 通过在 Apple 的 App Store 中发布应用程序，可将其提供给全球的客户。

> [!IMPORTANT]
> Apple [已表明](https://developer.apple.com/ios/submit/)，从 2019 年 3 月开始，提交到 App Store 的所有应用和更新必须是使用 iOS 12.1 SDK 或更高版本构建的，包含在 Xcode 10.1 或更高版本中。
> 应用还应支持 iPhone XS 和 12.9 英寸 iPad Pro 屏幕大小。

> [!IMPORTANT]
> 使用 Xamarin.Forms 时，若要查看关于 `UIWebView` 的弃用警告 (ITMS-90809) 的相关资源，请参阅 [Xamarin.Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809) 文档。

就像开发应用程序一样，分发应用程序需要使用相应的*配置文件*对应用程序进行设置。 配置文件是一种文件，其中包含了代码签名信息，以及应用程序标识和预期分发机制。 配置文件还包括可部署应用以实现非 App Store 分发的设备的信息。

> [!IMPORTANT]
> 必须加入个人或组织的 Apple 开发人员计划，才可使用 iTunes Connect 并将应用发布到 App Store  。 如果是 Apple Developer **Enterprise** Program 的成员，则不能按照此页上的步骤执行操作。

<a name="provisioning" />

## <a name="provisioning-an-app-for-app-store-distribution"></a>设置应用进行 App Store 分发

无论计划如何发布 Xamarin.iOS 应用程序，都需要生成特定于其的*分发配置文件*。 此配置文件允许对要发布的应用程序进行数字签名，使其可安装在 iOS 设备上。 类似于开发配置文件，分发配置文件包含以下内容：

- 应用程序 ID
- 分发证书

可选择与用于开发配置文件相同的**应用程序 ID** 和**设备**，但是如果还没有，则需要创建一个分发证书，以在将应用提交到 App Store 时，确认你的组织。 下面的部分将介绍创建分发证书的步骤。

> [!NOTE]
> 只有团队代理和管理员可创建分发证书和预配配置文件。

<a name="creatingcertificate" />

## <a name="creating-a-distribution-certificate"></a>创建分发证书

1. 浏览到 Apple Developer Member Center 的“证书、标识符和描述文件”  部分。
2. 在“证书”  下选择“生产”  。
3. 单击“+”  按钮创建新证书。
4. 在“生产”  标题下，选择“App Store 和临时”  ：

    [![](images/createcertmanually01.png "Select App Store and Ad Hoc")](images/createcertmanually01.png#lightbox)
5. 单击“继续”  ，再按照说明操作，通过密钥链访问创建证书签名请求：

    [![](images/createcertmanually02.png "Create a Certificate Signing Request via Keychain Access")](images/createcertmanually02.png#lightbox)
6. 在按照指示创建 CSR 后，单击“继续”  ，再将 CSR 上传到 Member Center：

    [![](images/createcertmanually03.png "Upload the CSR to the Member Center")](images/createcertmanually03.png#lightbox)

7. 单击“生成”  创建证书。
8. 最后，**下载**完成的证书，双击该文件进行安装。
9. 此时，证书应已在计算机上安装，但可能需要[刷新配置文件](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)，确保其在 Xcode 中可见。

或者，可通过 Xcode 中的”首选项”对话框请求证书。 为此，请执行以下步骤：

1. 选择团队，然后单击“管理证书...”  ：[![](images/selectteam.png "选择团队和“查看详细信息”")](images/selectteam.png#lightbox)

2. 然后，单击“iOS 分发证书”  旁的“创建”  按钮：[![](images/selectcert.png "创建 iOS 分发证书")](images/selectcert.png#lightbox)

3. 将根据你的团队特权生成签名标识（如下所示），或者可能需要等待团队代理或管理员批准：[![](images/generated.png "将生成签名标识并显示一个对话框")](images/generated.png#lightbox)

<a name="creatingprofile" />

## <a name="creating-a-distribution-profile"></a>创建分发配置文件

<a name="creatingappid" />

### <a name="creating-an-app-id"></a>创建应用 ID

与创建的其他任何配置文件一样，需要应用程序 ID 来标识将分发到用户设备的应用。 如果尚未创建此项，请按照以下步骤创建：

1. 在 [Apple Developer Center](https://developer.apple.com/account/overview.action) 中，浏览到“证书、标识符和描述文件”  部分。 在“标识符”  下选择“应用 ID”  。
2. 单击“+”  按钮并提供“名称”  ，该名称将在门户中标识它。
3. 应用前缀需已设置为你的团队 ID 且不可更改。 选择显式或通配符应用程序 ID，并以反向 DNS 格式输入捆绑 ID，如：
    - **Explicit**: com.[DomainName].[AppName]
    - **Wildcard**:com.[DomainName].*
4. 选择应用要求的任何[应用服务](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services)。
5. 单击“继续”  按钮，按照屏幕上的说明创建新的应用程序 ID。

### <a name="creating-a-provisioning-profile"></a>创建预配配置文件

拥有创建分发配置文件所需的必要组件后，请按照下面的步骤进行创建：

1. 返回 Apple 预配门户，选择“预配”   > “分发”  ：

    [![](images/distribute01.png "RSelect Provisioning > Distribution")](images/distribute01.png#lightbox)

2. 单击“+”  按钮，选择要创建为“App Store”  的分发配置文件的类型：

    [![](images/distribute02.png "Create an App Store distribution profile")](images/distribute02.png#lightbox)

3. 单击“继续”  按钮，然后在下拉列表中选择要为其创建分发配置文件的应用程序 ID：

    [![](images/distribute03.png "Select App ID from the dropdown list")](images/distribute03.png#lightbox)

4. 单击“继续”  按钮，再选择对应用进行签名所需的证书：

    [![](images/distribute04.png "Select the certificate required to sign the application")](images/distribute04.png#lightbox)

5. 单击“继续”  按钮，选择允许运行 Xamarin.iOS 应用的 .iOS 设备：

    [![](images/distribute05.png "Select the iOS devices that app will be allowed to run on")](images/distribute05.png#lightbox)

6. 单击“继续”  按钮，为新的分发配置文件输入“名称”  ：

    [![](images/distribute06.png "Enter a Name for the new Distribution Profile")](images/distribute06.png#lightbox)

7. 单击“生成”  按钮，创建新的配置文件并完成该过程。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 可能需要退出 Visual Studio for Mac，并让 Xcode 刷新可用签名标识和预配配置文件列表（按照[请求签名标识](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)部分中的说明操作），然后 Visual Studio for Mac 中才会有新的分发配置文件。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 可能需要退出 Visual Studio，并让 Xcode（在生成主机的 Mac 上）刷新可用签名标识和预配配置文件列表（按照[请求签名标识](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)部分中的说明操作），然后 Visual Studio 中才会有新的分发配置文件。

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>在 Xamarin.iOS 项目中选择分发配置文件

如果准备好最终生成用于在 iTunes App Store 中销售的 Xamarin.iOS 应用程序，可选择上面创建的分发配置文件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 在 Visual Studio for Mac 中，执行以下操作：

1. 在“解决方案资源管理器”  中，双击项目名称，将其打开进行编辑。
2. 在“配置”  下拉列表中，选择“iOS 应用程序包签名”  和“发布 | iPhone”  ：

    ![](images/releasexs01.png "Select Release | iPhone from the Configuration dropdown")
3. 在大多数情况下，可以保留“签名标识”  和“预配配置文件”  的默认值（即“自动”  ），Visual Studio for Mac 会根据 Info.plist 中的应用程序包标识符选择正确的配置文件：

    ![](images/releasexs02.png "The Signing Identity and the Provisioning Profile set to the default values of Automatic")
4. 如果需要，在下拉列表中，选择上面创建的签名标识和分发配置文件：

    ![](images/releasexs03.png "Select the Signing Identity and Distribution Profiles")
5. 单击“确定”  按钮保存更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 在 Visual Studio 中，执行以下操作：

1. 在“解决方案资源管理器  ”中，右键单击项目名称，然后选择“属性”  ，将其打开并进行编辑。
2. 在“配置”  下拉列表中，选择“iOS 应用程序包签名”  和“发布 | iPhone”  ：

    ![](images/releasevs01.png "Select Release | iPhone from the Configuration dropdown")
3. 在大多数情况下，可以保留“签名标识”  和“预配配置文件”  的默认值（即“自动”  ），Visual Studio 会根据 Info.plist 中的应用程序包标识符选择正确的配置文件

    ![](images/releasevs02.png "The Signing Identity and the Provisioning Profile set to the default values of Automatic")
4. 如果需要，在下拉列表中，选择上面创建的签名标识和分发配置文件：

    ![](images/releasevs03.png "Select the Signing Identity and Distribution Profile")
5. 保存对项目属性所做的更改。

-----

<a name="itunesconnect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中配置应用程序

成功预配应用后，下一步是在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中配置应用。iTunes Connect 是一套可管理 App Store 中 iOS 应用等的 Web 工具。

Xamarin.iOS 应用程序需要在 iTunes Connect 中进行恰当安装并配置，然后才能提交到 Apple 进行审核并最终在 App Store 作为付费或免费应用发布。

有关详细信息，请参阅[在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)文档。

<a name="submitting" />

## <a name="submitting-an-app-to-itunes-connect"></a>将应用提交到 iTunes Connect

使用分发配置文件对应用程序进行签名并在 iTunes Connect 中创建应用后，可将应用程序二进制文件上传到 Apple，供其审核。 成功通过 Apple 的审核后，即可将其发布到 App Store。

若要深入了解如何将应用程序发布到 App Store，请参阅[发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)。

<a name="windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>自动将 .app 应用程序包复制回 Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="summary"></a>总结

本文介绍了一些关键组件，用于准备 Xamarin.iOS 应用程序以在 App Store 中分发。

## <a name="related-links"></a>相关链接

- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Ad Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)
