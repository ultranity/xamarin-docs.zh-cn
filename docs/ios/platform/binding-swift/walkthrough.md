---
title: 演练：绑定 iOS Swift 库
description: 本文提供了针对现有 Swift framework Gigya 创建 Xamarin iOS 绑定的动手演练。 它介绍了在 Xamarin iOS 应用程序中编译 Swift 框架、绑定和使用绑定等主题。
ms.prod: xamarin
ms.assetid: B563ADE9-D0E3-4BC3-A288-66D2296BE706
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: b650f86a1bba62d5db7463875de3398db9c33842
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77497982"
---
# <a name="walkthrough-bind-an-ios-swift-library"></a>演练：绑定 iOS Swift 库

Xamarin 使移动开发人员能够使用 Visual Studio 和C#创建跨平台的本机移动体验。 你可以使用现成的 iOS 平台 SDK 组件。 但在许多情况下，你还希望使用为该平台开发的第三方 Sdk，Xamarin 允许你通过绑定进行操作。 若要将第三方目标 C 框架合并到你的 Xamarin iOS 应用程序中，你需要为其创建 Xamarin iOS 绑定，然后才能在应用程序中使用它。

IOS 平台及其本机语言和工具一直在不断发展，Swift 是 iOS 开发世界上最动态的领域之一。 有许多第三方 Sdk，它们已从目标-C 迁移到 Swift，并为我们带来了新的挑战。 即使 Swift 绑定过程与目标-C 类似，它也需要额外的步骤和配置设置，才能成功生成并运行可 AppStore 的 Xamarin 应用程序。

本文档的目的是概述对此方案进行寻址的高级方法，并提供一个简单示例的详细分步指南。

## <a name="background"></a>背景

Swift 最初是由 Apple 在2014中引入的，现已在版本5.1 上，第三方框架的采用迅速增长。 您可以使用几个选项来绑定 Swift 框架，本文档概述了使用客观-C 生成的接口标头的方法。 创建框架时，Xcode 工具会自动创建标题，并将其用作从托管世界向 Swift 环境进行通信的方式。

## <a name="prerequisites"></a>必备条件

为了完成本演练，您需要：

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [目标 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started#installing-objective-sharpie)
- [APPCENTER CLI](https://docs.microsoft.com/appcenter/test-cloud/) （可选）

## <a name="build-a-native-library"></a>生成本机库

第一步是生成启用了客观-C 标头的本机 Swift 框架。 该框架通常由第三方开发人员提供，并在以下目录中嵌入到包中： **\<FrameworkName > framework/header/\<FrameworkName >**

此标头公开公共接口，这些接口将用于创建 Xamarin iOS 绑定元数据并生成C#公开 Swift 框架成员的类。 如果标头不存在或者具有不完整的公共接口（例如，您不能看到类/成员），则有两个选项：

- 更新 Swift 源代码以生成标题，并将所需成员标记 `@objc` 特性
- 构建一个代理框架，你可以在其中控制公共接口并代理对基础框架的所有调用

在本教程中，我们将介绍第二种方法，因为它具有的依赖项对第三方源代码的依赖项并不始终可用。 避免第一种方法的另一个原因是支持将来框架更改所需的额外工作。 开始将更改添加到第三方源代码后，你将负责支持这些更改，并可能会将其与每个将来的更新合并。

例如，在本教程中，将创建[Gigya SWIFT SDK](https://developers.gigya.com/display/GD/Swift+SDK)的绑定：

1. 打开 Xcode 并创建新的 Swift 框架，该框架将是 Xamarin 代码和第三方 Swift 框架之间的代理。 单击 "**文件" > 新建 > 项目**"，然后按照向导步骤操作：

    ![xcode 创建框架项目](walkthrough-images/xcode-create-framework-project.png)

    ![xcode 名称框架项目](walkthrough-images/xcode-name-framework-project.png)

1. 从开发人员网站下载[Gigya framework](https://developers.gigya.com/display/GD/Swift+SDK)并将其解压缩。 撰写本文时，最新版本是[Gigya SWIFT SDK 1.0.9](https://downloads.gigya.com/predownload?fileName=Swift-Core-framework-1.0.9.zip)

1. 从项目文件资源管理器中选择**SwiftFrameworkProxy** ，然后选择 "常规" 选项卡

1. 将**Gigya**包拖放到 "常规" 选项卡下的 "Xcode 框架和库" 列表中，并在添加框架时选中 "**需要时复制项**" 选项：

    ![xcode 复制框架](walkthrough-images/xcode-copy-framework.png)

    验证 Swift 框架是否已添加到项目中，否则以下选项将不可用。

1. 确保选中 "不**嵌入**" 选项，这将在以后手动进行控制：

    [![xcode donotembed 选项](walkthrough-images/xcode-donotembed-option.png)](walkthrough-images/xcode-donotembed-option.png#lightbox)

1. 确保 "生成设置" 选项**始终嵌入 Swift 标准库**，其中包含框架的 swift 库，并将其设置为 "否"。 它将在以后手动控制，它将 dylib 包含在最终包中：

    [![xcode always 嵌入 false 选项](walkthrough-images/xcode-alwaysembedfalse-option.png)](walkthrough-images/xcode-alwaysembedfalse-option.png#lightbox)

1. 确保**启用 Bitcode**选项设置为 "**否**"。 目前为止，Xamarin 不包括 Bitcode，而 Apple 要求所有库都支持相同的体系结构：

    [![xcode enable bitcode false 选项](walkthrough-images/xcode-enablebitcodefalse-option.png)](walkthrough-images/xcode-enablebitcodefalse-option.png#lightbox)

    可以通过对框架运行以下终端命令来验证结果框架是否已禁用 Bitcode 选项：

    ```bash
    otool -l SwiftFrameworkProxy.framework/SwiftFrameworkProxy | grep __LLVM
    ```

    否则，输出应为空，否则您需要查看特定配置的项目设置。

1. 确保 "**目标-C 生成的接口头名称**" 选项已启用，并指定标头名称。 默认名称是 **\<FrameworkName >** ：

    [![xcode objectice-c header enabled 选项](walkthrough-images/xcode-objcheaderenabled-option.png)](walkthrough-images/xcode-objcheaderenabled-option.png#lightbox)

1. 公开所需的方法，并使用 `@objc` 特性标记这些方法，并应用以下定义的其他规则。 如果不使用此步骤生成框架，则生成的目标 C 标头将为空，并且 Xamarin 将无法访问 Swift 框架成员。 通过创建新的 Swift 文件**SwiftFrameworkProxy**并定义以下代码，公开基础 GIGYA Swift SDK 的初始化逻辑：

    ```swift
    import Foundation
    import UIKit
    import Gigya

    @objc(SwiftFrameworkProxy)
    public class SwiftFrameworkProxy : NSObject {

        @objc
        public func initFor(apiKey: String) -> String {
            Gigya.sharedInstance().initFor(apiKey: apiKey)
            let gigyaDomain = Gigya.sharedInstance().config.apiDomain
            let result = "Gigya initialized with domain: \(gigyaDomain)"
            return result
        }
    }
    ```

    有关上述代码的几个重要说明：

    - 从原始第三方 Gigya SDK 将 Gigya 模块导入此处，现在可以访问该框架的任何成员。
    - 用指定名称的 `@objc` 特性标记 SwiftFrameworkProxy 类，否则将生成唯一的不可读名称，如 `_TtC19SwiftFrameworkProxy19SwiftFrameworkProxy`。 应明确定义类型名称，因为稍后将使用其名称。
    - 从 `NSObject`中继承代理类，否则不会在目标 C 头文件中生成。
    - 将作为 `public`公开的所有成员标记为。

1. 将方案生成配置从 "**调试**" 更改为 "**发布**"。 为此，请打开 " **Xcode > 目标 >" 编辑方案**"对话框，然后将"**生成配置**"选项设置为"**发布**"：

    ![xcode 编辑方案](walkthrough-images/xcode-edit-scheme.png)

    ![xcode 编辑方案版本](walkthrough-images/xcode-edit-scheme-release.png)

1. 此时，已准备好创建框架。 构建模拟器和设备体系结构的框架，然后将输出合并为单个框架包。 识别已安装的 SDK 版本，以便使用**xcodebuild**工具生成源代码：

    ```bash
    xcodebuild -showsdks
    ```

    输出将如下所示：

    ```bash
    iOS SDKs:
            iOS 13.2                        -sdk iphoneos13.2
    iOS Simulator SDKs:
            Simulator - iOS 13.2            -sdk iphonesimulator13.2
    macOS SDKs:
            DriverKit 19.0                  -sdk driverkit.macosx19.0
            macOS 10.15                     -sdk macosx10.15
    tvOS SDKs:
            tvOS 13.2                       -sdk appletvos13.2
    tvOS Simulator SDKs:
            Simulator - tvOS 13.2           -sdk appletvsimulator13.2
    watchOS SDKs:
            watchOS 6.1                     -sdk watchos6.1
    watchOS Simulator SDKs:
            Simulator - watchOS 6.1         -sdk watchsimulator6.1
    ```

    选择所需的 iOS SDK 和 iOS 模拟器 SDK 版本（在本例中为版本13.2），并使用以下命令执行生成

    ```bash
    xcodebuild -sdk iphonesimulator13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    xcodebuild -sdk iphoneos13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    ```

    > [!TIP]
    > 如果有工作区而不是 project，请生成工作区，并将目标指定为必需的参数。 还需要指定输出目录，因为对于工作区，此目录将不同于项目生成。

    > [!TIP]
    > 你还可以使用[帮助器脚本](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L3-L14)来构建适用于所有适用体系结构的框架，或者仅在目标选择器中通过 Xcode 切换模拟器和设备生成它。

1. 有两个 Swift 框架，一个用于每个平台，可将它们合并为一个包，以便以后嵌入到 Xamarin iOS 绑定项目。 若要创建同时组合这两种体系结构的 fat 框架，需执行以下步骤。 框架包只是一个文件夹，因此你可以执行各种类型的操作，例如添加、删除和替换文件：

    - 使用**iphoneos**和**iphonesimulator**子文件夹导航到生成输出文件夹，并将其中一个框架复制为最终输出（fat framework）的初始版本。

        ```bash
        cp -R "Release-iphoneos" "Release-fat"
        ```

    - 将另一个生成中的模块与 fat framework 模块合并

        ```bash
        cp -R "Release-iphonesimulator/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/" "Release-fat/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/"
        ```

    - 将 iphoneos + iphonesimulator 配置合并为 fat framework

        ```bash
        lipo -create -output "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphoneos/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphonesimulator/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

    - 验证结果

        ```bash
        lipo -info "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

        输出应呈现以下内容，以反映框架的名称和包含的体系结构：

        ```bash
        Architectures in the fat file: Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy are: x86_64 arm64
        ```

    > [!TIP]
    > 如果你只想支持单个平台（例如，你正在构建只能在设备上运行的应用），则可以跳过创建 fat 库的步骤，并使用前面的设备生成中的输出框架。

    > [!TIP]
    > 你还可以使用[帮助器脚本](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L16-L24)创建 fat 框架，该框架将自动执行上述所有步骤。

## <a name="prepare-metadata"></a>准备元数据

此时，你应该有了一个框架，其中的目标为-C 生成的接口标头已准备就绪，可供 Xamarin iOS 绑定使用。  下一步是准备用于生成C#类的绑定项目所使用的 API 定义接口。 可以手动或通过[目标 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)工具和生成的标头文件自动创建这些定义。 使用 Sharpie 生成元数据：

1. 从官方下载网站下载最新的[客观 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)工具，并按照向导进行安装。 安装完成后，可以通过运行 sharpie 命令进行验证：

    ```bash
    sharpie -v
    ```

1. 使用 sharpie 和自动生成的目标 C 头文件生成元数据：

    ```bash
    sharpie bind --sdk=iphoneos13.2 --output="XamarinApiDef" --namespace="Binding" --scope="Release-fat/SwiftFrameworkProxy.framework/Headers/" "Release-fat/SwiftFrameworkProxy.framework/Headers/SwiftFrameworkProxy-Swift.h"
    ```

    输出反映了所生成的元数据文件： **ApiDefinitions.cs**和**StructsAndEnums.cs**。 保存这些文件以进行下一步，将它们包含在一个 Xamarin iOS 绑定项目和本机引用中：

    ```bash
    Parsing 1 header files...
    Binding...
        [write] ApiDefinitions.cs
        [write] StructsAndEnums.cs
    ```

    该工具将为C#每个公开的目标 C 成员生成元数据，类似于以下代码。 正如您所看到的，可以手动定义它，因为它具有用户可读的格式和简单的成员映射：

    ```csharp
    [Export ("initForApiKey:")]
    string InitForApiKey (string apiKey);
    ```

    > [!TIP]
    > 如果更改了标头名称的默认 Xcode 设置，则标头文件的名称可能不同。 默认情况下，它具有具有 **-Swift**后缀的项目的名称。 您始终可以通过导航到框架包的标头文件夹来检查该文件及其名称。

    > [!TIP]
    > 作为自动化过程的一部分，你可以使用[帮助器脚本](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L35)在创建 fat framework 后自动生成元数据。

## <a name="build-a-binding-library"></a>生成绑定库

下一步是使用 Visual Studio 绑定模板创建 Xamarin iOS 绑定项目，添加所需的元数据和本机引用，然后生成项目以生成可执行的库：

1. 打开 Visual Studio for Mac 并创建新的 Xamarin iOS 绑定库项目，为其指定一个名称，在本例中为 SwiftFrameworkProxy 并完成向导。 Xamarin 绑定模板由以下路径定位： **iOS > 库 > 绑定库**：

    ![visual studio 创建绑定库](walkthrough-images/visualstudio-create-binding-library.png)

1. 删除现有的元数据文件**ApiDefinition.cs**和**Structs.cs** ，因为这些文件将完全被目标 Sharpie 工具生成的元数据替换。
1. 复制 Sharpie 生成的元数据在前面的步骤之一中，在 "属性" 窗口中选择以下生成操作： **ApiDefinitions.cs**文件的**ObjBindingApiDefinition**和**StructsAndEnums.cs**文件的**ObjBindingCoreSource** ：

    ![visual studio 项目结构元数据](walkthrough-images/visualstudio-project-structure-metadata.png)

    元数据本身使用C#语言描述每个公开的目标 C 类和成员。 您可以看到原始的目标 C 标头定义与C#声明一起显示：

    ```csharp
    // @interface SwiftFrameworkProxy : NSObject
    [BaseType (typeof(NSObject))]
    interface SwiftFrameworkProxy
    {
        // -(NSString * _Nonnull)initForApiKey:(NSString * _Nonnull)apiKey __attribute__((objc_method_family("none"))) __attribute__((warn_unused_result));
        [Export ("initForApiKey:")]
        string InitForApiKey (string apiKey);
    }
    ```

    尽管它是有效C#的代码，但它并不是按原样使用，而是由 Xamarin 工具用于基于此元C#数据定义生成类。 因此，你将获得一个同名的C#类，而不是使用接口 SwiftFrameworkProxy，而你的 Xamarin iOS 代码可以对其进行实例化。 此类获取您的元数据定义的方法、属性和其他成员，您将以某种C#方式调用。

1. 将本机引用添加到生成的早期 fat 框架，以及该框架的每个依赖项。 在这种情况下，请将 SwiftFrameworkProxy 和 Gigya framework 本机引用添加到绑定项目：

    - 若要添加本机框架引用，请打开查找器，然后导航到包含框架的文件夹。 将框架拖放到解决方案资源管理器中的 "本机引用" 位置下。 或者，您可以使用 "本机引用" 文件夹上的上下文菜单选项，然后单击 "**添加本机引用**" 以查找框架并添加它们：

    ![visual studio 项目结构本机引用](walkthrough-images/visualstudio-project-structure-nativerefs.png)

    - 更新每个本机引用的属性并查看三个重要选项：

        - 设置智能链接 = true
        - Set Force Load = false
        - 设置用于创建原始框架的框架列表。 在这种情况下，每个框架只有两个依赖关系： Foundation 和 UIKit。 将其设置为 "框架" 字段：

        ![visual studio nativeref 代理选项](walkthrough-images/visualstudio-nativeref-proxy-options.png)

        如果需要指定任何其他链接器标志，请在 "链接器标志" 字段中进行设置。 在这种情况下，请将其保留为空。

    - 需要时指定其他链接器标志。 如果绑定的库只公开了客观-C Api，但在内部使用 Swift，则可能出现如下问题：

        ```Console
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftQuartzCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCoreImage
        ```

        在本机库的绑定项目属性中，必须将以下值添加到链接器标志：

        ```Linker
        L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphonesimulator/ -L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphoneos -Wl,-rpath -Wl,@executable_path/Frameworks
        ```

        前两个选项（ `-L ...` 一个）告诉本机编译器查找 swift 库的位置。 本机编译器将忽略没有正确的体系结构的库，这意味着可以同时为模拟器库和设备库传递位置，使其适用于模拟器和设备生成（仅适用于 iOS 的路径是正确的;对于 tvOS 和 watchOS，必须更新这些设置。 一个缺点是，此方法要求正确的 Xcode 在/Application/Xcode.app 中，如果绑定库的使用者在不同位置具有 Xcode，则它将不起作用。 替代解决方案是将这些选项添加到可执行项目的 iOS 生成选项（`--gcc_flags -L... -L...`）中的其他 mtouch 参数。 第三个选项使本机链接器将 swift 库的位置存储在可执行文件中，以便 OS 可以找到它们。

1. 最后一个操作是生成库，并确保没有任何编译错误。 你经常会发现，客观 Sharpie 生成的绑定元数据将使用 `[Verify]` 属性进行批注。 这些属性指示您应该通过将绑定与原始目标 C 声明（将在绑定声明中的注释中提供）进行比较来验证目标 Sharpie 是否执行了正确的操作。 可以通过[以下链接](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)了解有关用特性标记的成员的详细信息。 生成项目后，它可供 Xamarin iOS 应用程序使用。

## <a name="consume-the-binding-library"></a>使用绑定库

最后一步是在 Xamarin iOS 应用程序中使用 Xamarin iOS 绑定库。 创建新的 Xamarin iOS 项目，添加对绑定库的引用，并激活 Gigya Swift SDK：

1. 创建 Xamarin iOS 项目。 您可以使用**iOS > 应用 > 单视图应用程序**作为起点：

    ![visual studio 应用新建](walkthrough-images/visualstudio-app-new.png)

1. 将绑定项目引用添加到之前创建的目标项目或 .dll。 将绑定库视为常规 Xamarin 类库：

    ![visual studio 应用引用](walkthrough-images/visualstudio-app-refs.png)

1. 更新应用程序的源代码，并将初始化逻辑添加到主 ViewController，后者将激活 Gigya SDK

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
        var proxy = new SwiftFrameworkProxy();
        var result = proxy.InitForApiKey("APIKey");
        System.Diagnostics.Debug.WriteLine(result);
    }
    ```

1. 创建一个名为 " **btnLogin** " 的按钮，并添加以下按钮单击 "处理程序" 以激活身份验证流：

    ```csharp
    private void btnLogin_Tap(object sender, EventArgs e)
    {
        _proxy.LoginWithProvider(GigyaSocialProvidersProxy.Instagram, this, (result, error) =>
        {
            // process your login result here
        });
    }
    ```

1. 运行应用程序，请在调试输出中看到以下行： `Gigya initialized with domain: us1.gigya.com`。 单击按钮以激活身份验证流：

    [![swift 代理结果](walkthrough-images/swiftproxy-result.png)](walkthrough-images/swiftproxy-result.png#lightbox)

祝贺你！ 你已成功创建了一个 Xamarin iOS 应用和一个使用 Swift 框架的绑定库。 上述应用程序将在 iOS 12.2 + 上成功运行，因为从此 iOS 版本[Apple 开始引入 ABI 稳定性](https://swift.org/blog/swift-5-1-released/)，并且每个 iOS 启动 12.2 +，都包含 swift 运行时库，可用于运行使用 swift 5.1 + 编译的应用程序。 如果需要添加对早期 iOS 版本的支持，需要完成以下几个步骤：

1. 为了添加对 iOS 12.1 和更早版本的支持，你需要提供用于编译框架的特定 Swift dylib。 使用[SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) NuGet 包，通过 IPA 处理和复制所需的库。 将 NuGet 引用添加到目标项目并重新生成应用程序。 不需要执行任何其他步骤，NuGet 包将安装特定任务，这些任务是在生成过程中执行的，用于识别所需的 Swift dylib 并将其与最终 IPA 一起打包。

1. 若要将应用提交到应用商店，你要使用 Xcode 和 distributed 选项，该选项将更新 IPA 文件和**SwiftSupport**文件夹 dylib，以便 AppStore 接受此应用：

    ○存档应用。 从 "Visual Studio for Mac" 菜单中选择 "**生成 > 存档" 进行发布**：

    ![用于发布的 visual studio 存档](walkthrough-images/visualstudio-archiveforpublishing.png)

    此操作可生成项目并将其用于管理器，可通过 Xcode 访问该项目。

    ○通过 Xcode 分发。 打开 Xcode 并导航到 " **> 组织**程序" 菜单选项的窗口：

    ![visual studio 存档](walkthrough-images/visualstudio-archives.png)

    选择上一步骤中创建的存档，然后单击 "分发应用程序" 按钮。 按照向导将应用程序上传到 AppStore。

1. 此步骤是可选的，但验证应用是否可以在 iOS 12.1 及更早版本以及12.2 上运行非常重要。 可以通过 Test Cloud 和 UITest framework 来实现此目的。 创建一个 UITest 项目和一个运行该应用程序的基本 UI 测试：

    - 为 Xamarin iOS 应用程序创建 UITest 项目并进行配置：

        ![visual studio uitest new](walkthrough-images/visualstudio-uitest-new.png)

        > [!TIP]
        > 可以通过[以下链接](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)了解有关如何创建 UITest 项目并为应用程序配置该项目的详细信息。

    - 创建用于运行应用程序的基本测试，并使用一些 Swift SDK 功能。 此测试将激活应用程序，尝试登录，然后按 "取消" 按钮：

        ```csharp
        [Test]
        public void HappyPath()
        {
            app.WaitForElement(StatusLabel);
            app.WaitForElement(LoginButton);
            app.Screenshot("App loaded.");
            Assert.AreEqual(app.Query(StatusLabel).FirstOrDefault().Text, "Gigya initialized with domain: us1.gigya.com");

            app.Tap(LoginButton);
            app.WaitForElement(GigyaWebView);
            app.Screenshot("Login activated.");

            app.Tap(CancelButton);
            app.WaitForElement(LoginButton);
            app.Screenshot("Login cancelled.");
        }
        ```

        > [!TIP]
        > 通过[以下链接](https://docs.microsoft.com/appcenter/test-cloud/uitest/)了解有关 uitest FRAMEWORK 和 UI 自动化的详细信息。

    - 在 app center 中创建 iOS 应用，使用新设备集创建新的测试运行以运行测试：

        ![visual studio 应用中心新增](walkthrough-images/visualstudio-appcenter-new.png)

        > [!TIP]
        > 通过[以下链接](https://docs.microsoft.com/appcenter/test-cloud/)了解有关 AppCenter Test Cloud 的详细信息。

    - 安装 appcenter CLI

        ```bash
        npm install -g appcenter-cli
        ```

        > [!IMPORTANT]
        > 请确保已安装 node 6.3 或更高版本

    - 使用以下命令运行测试。 此外，请确保 appcenter 命令行当前已登录。

        ```bash
        appcenter test run uitest --app "Mobile-Customer-Advisory-Team/SwiftBinding.iOS" --devices a7e7cb50 --app-path "Xamarin.SingleView.ipa" --test-series "master" --locale "en_US" --build-dir "Xamarin/Xamarin.SingleView.UITests/bin/Debug/"
        ```

    - 验证结果。 在 AppCenter 门户中，导航到 **> 测试 > 测试运行的应用**：

        ![visual studio appcenter uitest 结果](walkthrough-images/visualstudio-appcenter-uitest-result.png)

        然后选择所需的测试运行并验证结果：

        ![visual studio appcenter uitest 运行](walkthrough-images/visualstudio-appcenter-uitest-runs.png)

你已开发了一个基本的 Xamarin iOS 应用程序，该应用程序通过 Xamarin 绑定库使用本机 Swift 框架。 该示例提供了一种简单的方法来使用所选框架，在实际应用程序中，你将需要公开更多 Api 并为这些 Api 准备元数据。 用于生成元数据的脚本将简化对框架 Api 的将来更改。

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
- [示例项目存储库](https://github.com/alexeystrakh/xamarin-binding-swift-framework)
