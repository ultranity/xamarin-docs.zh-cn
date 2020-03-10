---
title: Xamarin.iOS 的设备预配
description: 本文档介绍如何预配设备，以便将该设备用于测试应用程序。 其中还介绍了如何配置应用，以便使用推送通知等功能。
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: bb1ef1e948c796e6b0d91230be3c82c5a8c7d366
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910848"
---
# <a name="device-provisioning-for-xamarinios"></a>针对 Xamarin.iOS 的设备预配

开发 Xamarin.iOS 应用程序时，除了将应用部署到模拟器，还必须将其部署到物理设备进行测试。 通过在设备上运行，可以暴露出由于硬件限制（如内存或网络连接）而产生的硬件 Bug 和性能问题。 若要在物理设备上测试，必须*预配*设备，并且就设备测试事件通知 Apple。

下图突出显示部分展示了进行 iOS 设置所需的步骤：

[![](images/provisioningdiagram.png "The highlighted sections in this image show the steps required to get set up for iOS provisioning")](images/provisioningdiagram.png#lightbox)

接着，下一步是分发应用程序。 有关部署的详细信息，请访问[应用分发](~/ios/deploy-test/app-distribution/index.md)指南。

将应用程序部署到设备之前，需要具有 Apple 开发者计划的活动订阅或  使用[免费设置](~/ios/get-started/installation/device-provisioning/free-provisioning.md)。 Apple 提供两种计划选项：

- **Apple 开发者计划** – 无论你是个人用户还是代表组织，Apple 开发者计划都能让你开发、测试和分发应用。
- **Apple 企业开发者计划** – 企业计划最适合想要在内部开发和分发应用的组织。 企业计划的成员不能访问 iTunes 连接，且创建的应用不能发布到 App Store。

若要注册其中的任何计划，请访问 [Apple 开发者门户](https://developer.apple.com/programs/enroll/)进行注册。 注意，若要注册为 Apple 开发者，需要具有 [Apple ID](https://appleid.apple.com/)。 本指南假设你**是** Apple Developer Program 成员。

或者，Apple 在 Xcode 7 中引入了[免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)功能，可在单台设备上运行单个应用程序，而*无需*成为 Apple Developer Program 成员。 通过这种方式设置有多种限制，详细信息如[此处](~/ios/get-started/installation/device-provisioning/free-provisioning.md#limitations)所述。

设备上运行的应用程序均需要包含一组元数据（或*指纹*），其中内附应用程序和开发者信息。 Apple 使用该指纹来确保应用程序部署到用户设备或在用户设备上运行时不会被篡改。 由此需要应用开发者将其 Apple ID 注册为开发者、设置应用 ID、请求证书，并注册要部署应用程序的设备。

在设备上部署应用程序时，也会在 iOS 设备上安装设置配置文件。 设置配置文件用于验证生成时应用签名所用的信息以及由 Apple 通过密码签名的信息。 通过检查以下项，设置配置文件和指纹检查一起决定是否能将应用程序部署到设备：

- **对象**（证书 – 如果应用通过私钥进行签名，则哪个对象在配置文件中具有相应公钥？ 证书还关联开发者与开发团队）
- **事件**（单个应用 ID – Info.plist 中设置的捆绑标识符是否匹配配置文件中的应用 ID？）
- **位置**（设备 – 配置文件中是否包含设备？）

这些步骤用于确保开发过程中创建的或使用的所有内容（包括应用程序和设备）均可追溯到 Apple Developer 帐户。

## <a name="provisioning-your-device"></a>预配设备

预配 iOS 设备有两种方法：

- **自动（推荐）** - 选择项目中的“自动预配”方案，让 Visual Studio 自动创建和管理签名标识、应用 ID 和预配配置文件  。 有关如何自动管理预配的信息，请参阅[自动预配](automatic-provisioning.md)指南。 这是预配 iOS 设备的推荐方法。

- **手动** – 可以通过 Apple 开发人员门户创建和管理签名标识、应用 ID 和预配配置文件，如[手动预配](manual-provisioning.md)指南中所述。 然后对这些项目进行管理，如 [Apple 帐户管理](~/cross-platform/macios/apple-account-management.md)指南中所述。

## <a name="provisioning-for-application-services"></a>对应用程序服务进行预配

Apple 提供一系列精选的特殊应用服务（亦称为“功能”），可以为 Xamarin.iOS 应用激活这些服务。 必须同时在 iOS 设置门户（创建“应用 ID”  时）和“Entitlements.plist”  文件（它是 Xamarin.iOS 应用程序项目的一部分）上配置这些应用程序服务。 若要了解如何将应用程序服务添加到应用，请参阅[功能简介](~/ios/deploy-test/provisioning/capabilities/index.md)指南和[使用授权](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 创建具备所需应用服务的应用 ID。
- 创建含有此应用 ID 的新[配置文件](#provisioning-your-device)。
- 在 Xamarin.iOS 项目中设置权利

## <a name="related-links"></a>相关链接

- [可用设置](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [应用分发](~/ios/deploy-test/app-distribution/index.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
- [Apple - 应用分发指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
