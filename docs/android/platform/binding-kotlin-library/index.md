---
title: 绑定 Android Kotlin 库
description: 本文档介绍如何创建C#到 Kotlin 代码的绑定，从而可以在 Xamarin Android 应用程序中使用本机库。
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77497964"
---
# <a name="bind-android-kotlin-libraries"></a>绑定 Android Kotlin 库

Android 平台及其本机语言和工具不断发展，并使用最新产品/服务开发了大量第三方库。 最大程度地提高代码和组件重复使用是跨平台开发的主要目标之一。 当 Xamarin 开发人员不断增长，使用 Kotlin 生成的组件的功能越来越重要。 你可能已熟悉绑定常规[Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)库的过程。 现在可以使用其他文档来描述[绑定 Kotlin 库](walkthrough.md)的过程，以便 Xamarin 应用程序可以采用相同的方式使用它们。 本文档的目的是介绍为 Xamarin 创建 Kotlin 绑定的高级方法。

## <a name="high-level-approach"></a>高级方法

借助 Xamarin，你可以绑定任何第三方本机库，使其可供 Xamarin 应用程序使用。 Kotlin 是新语言并为用此语言构建的库创建绑定需要一些额外的步骤和工具。 此方法包括以下四个步骤：

1. 生成本机库
1. 准备 Xamarin 元数据，使 Xamarin 工具能够生成C#类
1. 使用本机库和元数据生成 Xamarin 绑定库
1. 在 Xamarin 应用程序中使用 Xamarin 绑定库

以下部分概述了这些步骤以及其他详细信息。

### <a name="build-the-native-library"></a>生成本机库

第一步是获取本机 Kotlin 库（AAR 包，它是 Android 存档）。 您可以直接从供应商请求或自行生成。

### <a name="prepare-the-xamarin-metadata"></a>准备 Xamarin 元数据

第二步是准备元数据转换文件，Xamarin 工具将使用该文件生成相应C#的类。 在最佳情况下，此文件可能为空，其中所有类均可通过 Xamarin 工具发现和生成。 在某些情况下，必须应用元数据转换才能生成正确和/或C#所需的代码。 在许多情况下，AAR 拆装器（例如[Java 反编译程序（JD））](http://java-decompiler.github.io/)必须用于标识隐藏的依赖项和要从要生成的C#类的最终列表中排除的不需要的类。 最终的元数据应表示引用 Xamarin 应用程序将与之交互的公共接口。

### <a name="build-a-xamarinandroid-binding-library"></a>生成 Xamarin Android 绑定库

第三步是创建一个特殊的项目-一个 Xamarin 的绑定库。 它包括 Kotlin 库作为本机引用和上一步中定义的元数据转换。 编写时，每个引用的 AAR 包都需要单独的 Android 绑定库项目。 绑定库必须添加[stdlib.h](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)包，才能支持 Kotlin 标准库。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 绑定库

第四个和最后一步是在 Xamarin Android 应用程序中引用绑定库。 通过添加对 Xamarin 类库绑定库的引用，Xamarin 应用程序可以使用该包中公开的 Kotlin 类。

## <a name="walkthrough"></a>演练

上述方法概括了为 Xamarin 创建 Kotlin 绑定所需的高级步骤。 在实践中，有许多较低级别的步骤，以及在准备这些绑定时要考虑的详细信息，包括适应本机工具和语言的变化。 目的是帮助您更深入地了解此概念和此过程中涉及的高级步骤。 有关详细的分步指南，请参阅[Xamarin Kotlin 绑定演练](walkthrough.md)文档。

## <a name="related-links"></a>相关链接

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安装](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 反编译程序](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 库](https://github.com/igalata/Bubble-Picker)
- [绑定 Java 库](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Java 绑定元数据](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Kotlin. Stdlib.h NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [示例项目存储库](https://github.com/xamcat/xamarin-binding-kotlin-framework)
