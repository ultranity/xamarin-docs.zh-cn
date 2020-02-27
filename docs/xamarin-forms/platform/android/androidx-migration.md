---
title: Xamarin 中的 AndroidX 迁移
description: 本文介绍 AndroidX 存在的原因，以及如何在 Xamarin 应用程序中迁移到 AndroidX。
ms.prod: xamarin
ms.assetid: 98884003-E65A-4EB4-842D-66CFE27344A4
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/22/2020
ms.openlocfilehash: 13fb802dec326cdb82bac8825ca84343ef85b13e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646652"
---
# <a name="androidx-migration-in-xamarinforms"></a>Xamarin 中的 AndroidX 迁移

AndroidX 替换 Android 支持库。 本文介绍了 AndroidX 的原因、其对 Xamarin 的影响，以及如何将应用程序迁移为使用 AndroidX 库。

## <a name="history-of-androidx"></a>AndroidX 的历史记录

创建 Android 支持库是为了在较早版本的 Android 上提供较新的功能。 它是一个兼容层，使开发人员能够使用在所有版本的 Android 操作系统上可能不存在的功能，并为旧版本正常回退。 支持库还包括便利和帮助器类、调试和实用工具工具以及复杂的类，这些类依赖于其他支持库类。

虽然支持库最初是单个二进制文件，但它已发展并发展为一组库，这对于新式应用程序的开发几乎非常重要。 以下是支持库中的一些常用功能：

- `Fragment` 支持类。
- 用于管理长列表的 `RecyclerView`。
- Multidex 对具有超过65536方法的应用的支持。
- `ActivityCompat` 类。

AndroidX 是支持库的替代，不再维护-所有新的库开发将在 AndroidX 库中进行。 AndroidX 是一种重新设计的库，它使用语义版本控制、更清晰的包名称和对常见应用程序体系结构模式的更好支持。 AndroidX 版本1.0.0 是等效于支持库版本28.0.0 的二进制文件。 有关从支持库到 AndroidX 的类映射的完整列表，请参阅支持 developer.android.com 上的[库类映射](https://developer.android.com/jetpack/androidx/migrate/class-mappings)。

Google 使用 AndroidX 创建了一个名为 Jetifier 的迁移过程。 Jetifier 将检查生成过程中的 jar 字节码，并在应用程序代码和依赖项中对其 AndroidX 等效项支持库引用。

在 Xamarin 应用程序中，就像在 Android Java 应用程序中一样，jar 依赖项必须迁移到 AndroidX。 但是，Xamarin 绑定还必须迁移，以指向正确的底层 jar 文件。 Xamarin. 添加了对版本4.5 中自动 AndroidX 迁移的支持。

有关 AndroidX 的详细信息，请参阅 developer.android.com 上的[AndroidX 概述](https://developer.android.com/jetpack/androidx)。

## <a name="automatic-migration-in-xamarinforms"></a>Xamarin 中的自动迁移

若要自动迁移到 AndroidX，Xamarin. Forms 项目必须：

- 面向 Android API 版本29或更高版本。
- 使用 Xamarin 版本4.5 或更高版本。

在项目中确认这些设置后，在 Visual Studio 2019 中生成 Android 应用。 在生成过程中，将检查中间语言（IL），并支持库依赖项和绑定与 AndroidX 依赖关系交换。 如果你的应用程序具有生成所需的所有 AndroidX 依赖项，则你将注意到生成过程没有任何区别。

> [!NOTE]
> 您必须将对支持库的引用保留在您的项目中。 它们用于编译应用程序，然后迁移过程将检查生成的 IL 并转换依赖项。

如果检测到的 AndroidX 依赖项不是项目的一部分，则会报告生成错误，指出缺少哪些 AndroidX 包。 示例生成错误如下所示：

```
Could not find 37 AndroidX assemblies, make sure to install the following NuGet packages:
- Xamarin.AndroidX.Lifecycle.LiveData
- Xamarin.AndroidX.Browser
- Xamarin.Google.Android.Material
- Xamarin.AndroidX.Legacy.Supportv4
You can also copy and paste the following snippit into your .csproj file:
 <PackageReference Include="Xamarin.AndroidX.Lifecycle.LiveData" Version="2.1.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Browser" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.Google.Android.Material" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Legacy.Support.V4" Version="1.0.0-rc1" />
```

缺少的 NuGet 包可以通过 Visual Studio 中的 NuGet 包管理器进行安装，也可以通过编辑你的 Android .csproj 文件来安装，以包含错误中列出的 `PackageReference` XML 项。

解析缺少的包后，重新生成项目将加载缺少的包，项目将使用 AndroidX 依赖项而不是支持库依赖项进行编译。

> [!NOTE]
> 如果你的项目和项目依赖项不引用 Android 支持库，迁移过程不会执行任何操作，并且不会执行。

## <a name="related-links"></a>相关链接

- Developer.android.com 上的[Android 支持库概述](https://developer.android.com/topic/libraries/support-library/index)
- Developer.android.com 上的[AndroidX 概述](https://developer.android.com/jetpack/androidx)
