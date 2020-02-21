---
title: 绑定 iOS Swift 库
description: 本文档介绍如何创建C#对 Swift 代码的绑定，使其能够在 Xamarin iOS 应用程序中使用本机库和 CocoaPods。
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 9a683f31016a9db4271e3909e421f27ef83c2080
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77497958"
---
# <a name="bind-ios-swift-libraries"></a>绑定 iOS Swift 库

IOS 平台及其本机语言和工具不断发展，并使用最新产品/服务开发了大量第三方库。 最大程度地提高代码和组件重复使用是跨平台开发的主要目标之一。 随着开发人员不断增长，使用 Swift 生成的组件的功能对 Xamarin 开发人员越来越重要。 您可能已熟悉了对常规[目标-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)库的绑定过程。 现在可以使用其他文档介绍[绑定 Swift 框架](walkthrough.md)的过程，以便 Xamarin 应用程序可以采用相同的方式使用它们。 本文档的目的是介绍为 Xamarin 创建 Swift 绑定的高级方法。

## <a name="high-level-approach"></a>高级方法

借助 Xamarin，你可以绑定任何第三方本机库，使其可供 Xamarin 应用程序使用。 Swift 是新语言并为用此语言构建的库创建绑定需要一些额外的步骤和工具。 此方法包括以下四个步骤：

1. 生成本机库
1. 准备 Xamarin 元数据，使 Xamarin 工具能够生成C#类
1. 使用本机库和元数据生成 Xamarin 绑定库
1. 在 Xamarin 应用程序中使用 Xamarin 绑定库

以下部分概述了这些步骤以及其他详细信息。

### <a name="build-the-native-library"></a>生成本机库

第一步是使用创建了客观-C 标头的本机 Swift 框架准备就绪。 此文件是一个自动生成的标头，该标头公开所需的 Swift 类、方法和字段，使目标C# -C 和最终通过 Xamarin 绑定库可对其进行访问。 此文件位于以下路径中的框架内： **\<FrameworkName > framework/FrameworkName/\<>** 如果公开的接口具有所有必需的成员，则可以跳到下一步。 否则，需要执行更多步骤才能公开这些成员。 此方法将取决于你是否有权访问 Swift 框架源代码：

- 如果你有权访问代码，则可以使用 `@objc` 特性来修饰必需的 Swift 成员，并应用一些其他规则，以让 Xcode 生成工具了解这些成员应公开给目标 C 环境和标头。
- 如果你没有源代码访问权限，则需要创建一个代理 Swift 框架，该框架包装原始 Swift 框架，并使用 `@objc` 特性定义应用程序所需的公共接口。

### <a name="prepare-the-xamarin-metadata"></a>准备 Xamarin 元数据

第二步是准备 API 定义接口，绑定项目使用这些接口来生成C#类。 这些定义可以手动创建，也可以由[目标 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)工具自动创建，也可以由上述自动生成的 **\<FrameworkName >** 。 生成元数据后，应手动对其进行验证和验证。

### <a name="build-the-xamarinios-binding-library"></a>生成 Xamarin iOS 绑定库

第三步是创建特殊的项目-Xamarin iOS 绑定库。 它引用在上一步准备的框架和元数据，以及相应框架所依赖的任何其他依赖项。 它还处理所引用的本机框架与使用的 Xamarin iOS 应用程序的链接。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 绑定库

第四步和最后一步是在 Xamarin iOS 应用程序中引用绑定库。 在面向 iOS 12.2 及更高版本的 Xamarin iOS 应用程序中，可以使用本机库。 对于以较低版本为目标的应用程序，需要执行一些额外的步骤：

- 为运行时支持添加 Swift .dylib 依赖项。 从 iOS 12.2 和 Swift 5.1 开始，语言变为 ABI （应用程序二进制接口）的稳定性和兼容性。 这就是，面向较低 iOS 版本的任何应用程序都需要包含框架使用的 Swift dylib 依赖关系。 使用[SwiftRuntimeSupport NuGet 包](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)自动将所需的 .dylib 依赖项包含到生成的应用程序包中。
- 添加带签名 dylib 的**SwiftSupport**文件夹，该文件夹在上载过程中由 AppStore 进行验证。 应使用 Xcode 工具对包进行签名并将其分发到 AppStore 连接，否则将自动拒绝此包。

## <a name="walkthrough"></a>演练

上述方法概述了为 Xamarin 创建 Swift 绑定所需的高级步骤。 在实践中，有许多较低级别的步骤，以及在准备这些绑定时要考虑的详细信息，包括适应本机工具和语言的变化。 目的是帮助您更深入地了解此概念和此过程中涉及的高级步骤。 有关详细的分步指南，请参阅[Xamarin Swift 绑定演练](walkthrough.md)文档。

## <a name="related-links"></a>相关链接

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [目标 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Sharpie 元数据验证](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [绑定目标-C 框架](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Gigya iOS SDK （Swift 框架）](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5.1 ABI 稳定性](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Xamarin UITest 自动化](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Xamarin UITest 配置](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter Test Cloud](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [示例项目存储库](https://github.com/xamcat/xamarin-binding-swift-framework)
