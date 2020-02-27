---
title: AndroidX
description: 如何使用 Xamarin 通过 AndroidX 开发应用程序。
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: 2836f2003a5b745b042ee6003a3d6a11b9139e44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618910"
---
# <a name="androidx-with-xamarin"></a>带 Xamarin 的 AndroidX

_如何使用 Xamarin 通过 AndroidX 开发应用程序。_

AndroidX 是原始 Android 支持库的主要改进，不再维护。 **AndroidX**包通过提供可在 android 应用程序中使用的功能奇偶校验和新库来完全替换 Android 支持库。

AndroidX 包括以下功能：

- AndroidX 中的所有包现在都具有一致的命名空间，从 `androidx`开始。 这意味着所有 Android 支持库包都映射到相应的 `androidx.*` 包。
- `androidx` 包单独维护和更新。 这意味着你可以相互独立地更新 AndroidX 库。
- 从 Android 支持库的 v28，将不再提供更多版本。 所有开发都将包含在 `androidx` 中。

![AndroidX 徽标](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>要求

以下列表是在基于 Xamarin 的应用中使用 AndroidX 功能所必需的：

- **Visual studio** -Windows update 上的 visual studio 2019 版本16.4 或更高版本。 在 macOS 上，更新为适用于 Mac 版本8.4 或更高版本的 Visual Studio 2019。
- **Xamarin** -xamarin 10.0 或更高版本必须随 Visual Studio 一起安装（在 Windows 上**通过 .net**工作负荷自动安装 xamarin，并作为**Visual Studio for Mac 安装程序**的一部分进行安装）
- **Java 开发人员工具包**-Xamarin 10.0 开发需要 JDK 8。 Microsoft 的 OpenJDK 分发版将作为 Visual Studio 的一部分自动安装。
- 必须通过 Android SDK 管理器安装**Android SDK** Android SDK API 28 或更高版本。

## <a name="get-started"></a>入门

你可以通过将任何[AndroidX NuGet 包](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)包含在 Android 项目中来开始使用 AndroidX。 详细了解如何在[Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)或[Visual Studio for Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)中安装和使用包

## <a name="behavior-changes"></a>行为变更

由于 AndroidX 是对 Android 支持库的重新设计，因此它包括将影响使用 Android 支持库生成的 Android 应用程序的迁移步骤。

### <a name="package-name-change"></a>包名称更改
新旧包之间的包名称已更改。 下面可以看到这些更改的示例：

| 原来                    | 新建                    |
| ---------------------- | ---------------------- |
| android. 支持。 * *     | androidx.@             |
| android. * *      | ".com"。@ |
| 支持. 测试。 * * | androidx。@       |
| android. * *        | androidx.@             |
| android. 聊天室. * * | androidx。@ |
| android. * * | androidx。@ |

有关包命名的详细信息，[请参阅以下文档](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings)。

## <a name="migration-tooling"></a>迁移工具

你需要为你的应用程序注意三个迁移步骤。

1. 如果你的应用程序包含**Android 支持库命名空间，并且你想要将它们迁移到 AndroidX 命名空间**，则可以使用**迁移到 AndroidX** IDE 工具来处理大多数命名空间方案。 

在 Visual Studio 2019 中通过工具 > 选项启用**AndroidX 迁移** **> Xamarin > Android 设置**（可以在 Visual Studio for Mac 上跳过此步骤）。

![启用 AndroidX 迁移迁移](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

右键单击你的项目并**迁移到 AndroidX**。

![迁移到 AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> 对于该工具不能涵盖的方案，你将需要进行一些手动命名空间更改。 虽然我们将为你映射正确的包，但建议你查看官方[项目映射](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings)和[类映射](https://developer.android.com/jetpack/androidx/migrate/class-mappings)，以帮助你的项目迁移。

2. 如果你的应用程序包含尚未**迁移到 AndroidX 命名空间的任何依赖项**，则必须使用[Android 支持库来 AndroidX 迁移包。](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. 如果你的应用程序不**包含任何需要 AndroidX 命名空间迁移的依赖项**，则可以[立即在 NuGet 上使用 AndroidX 库](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。

## <a name="troubleshooting"></a>故障排除

- AndroidX 中的某些体系结构包将与支持库版本冲突。 若要解决此问题，应使用这些包的 AndroidX 版本，并删除支持库版本。 例如，如果在项目中引用 `Xamarin.Android.Arch.Work.Runtime`，它将与新添加 `AndroidX.Work` 包的类型发生冲突。

## <a name="summary"></a>总结

本文介绍了 AndroidX 并说明了如何通过 AndroidX 安装和配置适用于 Xamarin 开发的最新工具和包。 其中提供了 AndroidX 的概述。 它包括指向 API 文档和 Android 开发人员主题的链接，以帮助你开始使用 AndroidX 创建应用。 它还重点介绍了可能影响现有应用的最重要的 AndroidX 行为更改和故障排除主题。

## <a name="related-links"></a>相关链接

- [AndroidX 简介 |Xamarin Show](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Xamarin AndroidX GitHub 存储库](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX 迁移 GitHub 存储库](https://github.com/xamarin/XamarinAndroidXMigration)
