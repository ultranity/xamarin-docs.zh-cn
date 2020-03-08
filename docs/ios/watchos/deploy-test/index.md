---
title: 部署和测试 Xamarin 的 watchOS 应用
description: 本文档介绍了如何部署和测试用 Xamarin 构建的 watchOS 应用。 它提供了一个部署清单，讨论了显式和通配符应用 Id，并查看了应用组。
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: b9b4d201e02d60bd6131c8693d9ac6a233e4fe10
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918738"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>部署和测试 Xamarin 的 watchOS 应用

## <a name="deployment-checklist"></a>部署清单

无论是要部署到测试监视还是上传到应用商店，都需要完成本页中的步骤：

- 在**IOS 开发人员中心**：
  - 已创建[应用 id](#App_IDs) 。
  - 配置的[应用程序组](#App_Groups)（如果需要）。
  - 已创建分发预配配置文件

- 在解决方案中：

  - 验证是否已设置[捆绑 id 和项目引用](~/ios/watchos/get-started/installation.md)。
  - 检查是否[已正确配置](~/ios/watchos/app-fundamentals/icons.md)图标。
  - 检查所有项目中的捆绑版本号匹配项。
  - 为应用组配置**info.plist** （如果需要）。

- 然后按照说明进行操作：
  - [部署到用于测试的 Apple Watch](~/ios/watchos/deploy-test/device.md)，或
  - [上载到 App Store](~/ios/watchos/deploy-test/appstore.md)。

<a name="App_IDs"/>

## <a name="app-ids"></a>应用 Id

如[安装说明](~/ios/watchos/get-started/installation.md)中所述，手表应用中的所有三个项目都有相关的捆绑包 id，例如：

- Xamarin iOS 统一项目-`com.xamarin.WatchKitCatalog`
- WatchKit 扩展项目-`com.xamarin.WatchKitCatalog.watchkitextension`
- 监视应用项目-`com.xamarin.WatchKitCatalog.watchkitapp`

所有三个项目都需要一个匹配的分发配置文件，使用为每个项目显式应用 Id 或使用通配符应用 ID。

### <a name="explicit-app-ids"></a>显式应用 Id

为每个项目的捆绑 ID 创建一个**应用 ID** （在 IOS 开发人员中心将如下所示）：

![IOS 开发人员中心中的捆绑 Id](images/appids-specific-sml.png)

创建或配置应用 Id 时，请记住启用应用需要的特定功能。 这可能包括推送通知和应用组。

需要为每个应用 ID 创建分发预配配置文件。

### <a name="wildcard-app-id"></a>通配符应用 ID

或者，你可以创建一个与所有三个项目（如 `com.xamarin.*`）匹配的通配符**应用 ID** 。

请注意，某些功能不能用于通配符应用 ID （例如推送通知）。 如果你的应用需要这些功能，则应创建显式应用 Id。

对于分发，你只需为通配符应用 ID 创建一个分发配置文件。

<a name="App_Groups" />

## <a name="app-groups"></a>应用组

可以使用应用组在 iOS 应用和监视扩展之间共享数据。 应确保解决方案具有以下内容：

- 在 Apple 开发人员门户**证书，标识符 & 配置文件**部分配置了**应用组**。

- 已在 iOS 应用和监视扩展的**应用 ID**和 info.plist **中启用***应用*组（并提供**应用组 ID**）。

### <a name="certificates-identifiers--profiles"></a>证书，标识符 & 配置文件

若要使用应用组，请在 "**应用组**" 屏幕中创建一个条目。 在下面的示例中，使用与应用 Id 相同的反向 DNS 样式来命名组，但使用 `group.` 前缀（必需）：

![标识符](images/appgroups-new-sml.png)

应用组将出现在列表中：

![标识符列表](images/appgroups-setup-sml.png)

创建组后，可以在**应用 ID**配置中对其进行引用。 请记住，同时包含 iOS 应用和监视扩展**应用 id**。

![可用配置](images/appgroups-sml.png)

请勿在 Apple Watch 的应用程序 ID**中启用应用**程序组。 不需要在监视本身上启用它。

### <a name="entitlementsplist"></a>Entitlements.plist

某些应用程序功能（例如 应用组）要求设置权利。
双击以在这些项目中编辑**info.plist**文件：

- iOS 应用项目
- 监视扩展项目

。![Info.plist 编辑器](images/entitlements-plist-sml.png)

不要在 "监视应用项目"**中启用权利**。 不需要在监视本身上启用它。

## <a name="related-links"></a>相关链接

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
