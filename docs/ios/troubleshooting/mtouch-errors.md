---
title: Xamarin iOS 错误
description: 本文档介绍 mtouch （用于捆绑 Xamarin iOS 应用程序的工具）生成的各种错误。 错误按代码和给定的完整说明列出。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: a26c83565e4cfa64272549e12a35206dff6ec3c0
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911568"
---
# <a name="xamarinios-errors"></a>Xamarin iOS 错误

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx： mtouch 错误消息

例如 参数、环境、缺少的工具。

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
  -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000：意外错误-请在 https://github.com/xamarin/xamarin-macios/issues/new 填写 bug 报告

出现意外错误。 请使用尽可能多的信息在[GitHub](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题，包括：

- 完整生成日志，具有最大详细级别（例如 `-v -v -v -v` 在**附加的 mtouch 参数**中）;
- 再现错误的最小测试用例;与
- 所有版本的

获取确切版本信息的最简单方法是使用 " **Visual Studio for Mac** " 菜单，**关于 Visual Studio for Mac**项，"**显示详细信息**" 按钮，然后复制/粘贴版本信息（可以使用 "**复制信息**" 按钮）。

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001：未提供任何特定于设备的操作而提供 "-devname"

这是一个警告，如果在未请求设备特定的操作（-logdev/-installdev/-killdev/-launchdev/-listapps）时将-devname 传递给 mtouch，则会发出此警告。

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002：无法分析环境变量 *。

如果尝试设置无效的环境键 = 值变量对，则会发生此错误。 正确的格式为： `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003：应用程序名称 "* .exe" 与 SDK 或产品程序集（.dll）名称冲突。

可执行程序集的名称和应用程序的名称不能与应用程序中任何 dll 的名称匹配。 请修改可执行文件的名称。

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004： New refcounting 逻辑还需要启用 SGen。

如果启用 refcounting 扩展，还必须在项目的 "iOS 生成选项" （"高级" 选项卡）中启用 SGen 垃圾回收器。

从 Xamarin 7.2.1 开始，已提升了此要求，可以同时启用 Boehm 和 SGen 垃圾收集器的新 refcounting 逻辑。

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005：输出目录 * 不存在。

请创建目录。

不会再生成此错误，如果该目录不存在，mtouch 将自动创建它。

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006：没有 unixodbc-devel 平台位于 *，请使用--platform = X-PLAT 来指定 SDK。

Xamarin 无法在错误消息中提到的位置找到 SDK 目录。 请验证路径是否正确。

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007：根程序集 * 不存在。

Xamarin 无法在错误消息中提到的位置找到程序集。 请验证路径是否正确。

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008：应仅提供一个根程序集，找到 # 个程序集： *。

向 mtouch 传递了多个根程序集，但只能有一个根程序集。

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009：加载程序集时出错： *。

从根程序集引用加载程序集时出错。 生成输出中可能提供详细信息。

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010：无法分析命令行参数： *。

分析命令行参数时出错。 请验证它们都是正确的。

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011： \* 是针对比 Monotouch.dialog 支持的更新的运行时（\*）生成的。

通常会报告此警告，因为项目引用了未使用 Xamarin BCL 生成的类库。

与使用 .NET 4.0 SDK 的应用程序相比，使用 .net SDK 的应用程序可能无法在仅支持 .NET 2.0 的系统上运行，而使用 .NET 4.0 构建的库可能无法在 Xamarin 上使用。

一般的解决方案是将库构建为 Xamarin 类库。 这可以通过创建新的 Xamarin 类库项目来实现，并向其添加所有源文件。 如果你没有库的源代码，你应该联系供应商，并请求他们提供与 Xamarin 兼容的库版本。

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012：提供完整 * 的不完整数据。

当前版本的 Xamarin 中不再报告此错误。

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013：分析支持还要求 sgen 启用。

如果启用了事件探查（--分析），则必须启用 SGen （--SGen）。

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014： iOS \* SDK 不支持构建以 \*为目标的应用程序。

在下列情况下可能会发生这种情况：

- 已启用 ARMv6 并已安装 Xcode 4.5 或更高版本。
- ARMv7s 已启用，且已安装 Xcode 4.4 或更早版本。

请验证安装的 Xcode 版本是否支持所选体系结构。

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x86_64--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015： ABI： * 无效。 支持的 Abi 包括： i386、x86_64、armv7、armv7 + llvm、armv7 + llvm + thumb2、armv7s、armv7s + llvm、armv7s + llvm + thumb2、arm64 和 arm64 + llvm。

向 mtouch 传递了无效的 ABI。 请指定有效的 ABI。

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016：选项 * 已弃用。

提及的 mtouch 选项已弃用，将被忽略。

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017：应提供根程序集。

生成应用时，需要指定根程序集（通常是主要可执行文件）。

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018：未知的命令行参数： *。

Mtouch 无法识别错误消息中提到的命令行参数。

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019：只能使用一个--[日志 | 安装 | kill | 启动] 开发或--[启动 | 调试] sim 选项。

不能同时使用多个 mtouch 选项：

- --logdev
- --installdev
- --killdev
- --launchdev
- --launchdebug
- --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020： "\*" 的有效选项为 "\*"。

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021：使用静态注册注册器时，不能使用 gcc/g + + （--gcc）进行编译（这是为设备进行编译时的默认设置）。 删除--use-gcc 标志或使用动态注册器（--注册程序： dynamic）。

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022：不支持的选项 "------in--in-

删除 `--unsupported--enable-generics-in-registrar` 和 `--registrar`的选项。 从 Xamarin 7.2.1 开始，默认注册机构支持泛型。

不再显示此错误（`--unsupported--enable-generics-in-registrar` 已从 mtouch 中删除命令行参数）。

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023：应用程序名称 "* .exe" 与其他用户程序集冲突。

可执行程序集的名称和应用程序的名称不能与应用程序中任何 dll 的名称匹配。 请修改可执行文件的名称。

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024：找不到所需的文件 "*"。

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025：未提供 SDK 版本。 请添加 `--sdk=X.Y` 以指定应该使用哪个 iOS SDK 来生成应用程序。

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026：无法分析命令行参数 "\*"： \*

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027：选项 "\*" 和 "\*" 不兼容。

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028：在面向 iOS 4.1 或更早版本时无法启用饼图（-饼图）。 请禁用饼图（-饼图： false），或将部署目标设置为至少 iOS 4。2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029：仅在模拟器（--sim）中支持复制（--启用-复制）。

仅当为模拟器构建时才支持复制。 这意味着，如果将 `--enable-repl` 传递给 mtouch，则还必须传递 `--sim`。

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030：可执行文件名称（\*）和应用名称（\*）不同，这可能会阻止崩溃日志正确地获取符号化。

如果 Xcode symbolicates （将内存地址转换为函数名称和文件/行号），则如果可执行文件和应用具有不同的名称（不含扩展名），则进程可能会失败。

若要解决此问题，请在项目的生成/iOS 应用程序选项中更改 "应用程序名称"，或在项目的 "生成/输出" 选项中更改 "程序集名称"。

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031：命令行参数 "--enable-get-help" 和 "--launchsim" 也需要 "--"。

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032：除非还指定了 "--debug"，否则将忽略选项 "--debugtrack"。

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033： Xamarin iOS 项目必须引用 monotouch.dialog 或 Xamarin。

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034：不能在同一 Xamarin 项目中同时包含 "monotouch.dialog" 和 "Xamarin."-"\*" 是显式引用的，而 "\*" 由 "*" 引用。

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036：无法为 \* 应用启动 \* 的模拟器。 请在项目的 "iOS 生成选项" （"高级" 页）中启用正确的体系结构。

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x86_64"></a>MT0037： monotouch.dialog 不兼容64位。 引用 Xamarin 或不生成64位体系结构（ARM64 和/或 x86_64）。

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038：引用 Xamarin 时不支持旧注册者（--注册员： oldstatic | olddynamic）。

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039：目标为 ARMv6 的应用程序无法引用 Xamarin。

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040：找不到 "\*" 引用的程序集 "\*"。

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041：不能同时引用 "monotouch.dialog" 和 "Xamarin. .dll"。

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042：未找到对 monotouch.dialog 或的引用。 将添加对 monotouch.dialog 的引用。

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043：引用 "Xamarin" 时，当前不支持 Boehm 垃圾回收器。 改为选择 SGen 垃圾回收器。

统一项目仅支持 SGen 垃圾回收器。 确保没有其他 mtouch 标志指定 Boehm 作为垃圾回收器。

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044：--listsim 仅在 Xcode 6.0 或更高版本中受支持。

安装更新的 Xcode 版本。

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045：--仅当使用 iOS 8.0 （或更高版本） SDK 时才支持扩展。

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047：统一应用程序的最低部署目标为5.1.1，当前部署目标为 "*"。 请在项目的 iOS 应用程序选项中选择一个较新的部署目标。

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>仅当部署目标为8.0 或更高版本时，才支持 MT0049： \*。 \* 功能可能无法正常工作。

部署目标所引用的 iOS 版本中不支持指定的框架。 请将部署目标更新为较新的 iOS 版本，或从应用中删除指定框架的使用情况。

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051： Xamarin \* 需要 Xcode 5.0 或更高版本。 当前 Xcode 版本（在 \*中 \*找到）是。

安装更新的 Xcode。

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052：未指定命令。

没有为 mtouch 指定任何操作。

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054：无法规范化路径 "\*"： \*

这是内部错误。 如果看到此错误，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055： Xcode 路径 "*" 不存在。

使用 `--sdkroot` 传递的 Xcode 路径不存在。 请指定有效的路径。

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056：在默认位置中找不到 Xcode （/Applications/Xcode.app）。 请安装 Xcode，或使用--sdkroot \<路径 > 传递自定义路径。

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057：无法从 sdk 根 "*" 确定指向 Xcode 的路径。 请指定 Xcode 捆绑包的完整路径。

使用 `--sdkroot` 传递的路径未指定有效的 Xcode 应用。 请指定 Xcode 应用的路径。

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058： Xcode "\*" 无效（文件 "\*" 不存在）。

使用 `--sdkroot` 传递的路径未指定有效的 Xcode 应用。 请指定 Xcode 应用的路径。

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059：在系统上找不到当前选择的 Xcode： *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060：在系统上找不到当前选择的 Xcode。 "xcode" 返回了 "*"，但该目录不存在。

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061：没有指定的 Xcode （使用--sdkroot），使用 "Xcode------------path" 报告的系统 Xcode： *

这是一个信息性警告，说明由于未指定任何 Xcode 将使用的。

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062：未指定 Xcode （使用--sdkroot 或 "Xcode"），请改用默认的 Xcode： *

这是一个信息性警告，说明由于未指定任何 Xcode 将使用的。

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063：在扩展中找不到可执行文件 * （其信息中没有 CFBundleExecutable 条目。 info.plist）

每个信息。 info.plist 必须有一个可执行文件（使用 CFBundleExecutable 项），但生成过程中应自动生成一个条目。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064： Xamarin 仅支持带有统一项目的嵌入式框架。

使用 Unified API 时，Xamarin 仅支持嵌入式框架;请更新项目以使用 Unified API。

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065：仅当部署目标至少为8.0 （当前部署目标： \* embedded framework： \*）时，Xamarin 才支持嵌入的框架

当部署目标至少为8.0 （因为较早版本的 iOS 不支持嵌入式框架）时，Xamarin 仅支持嵌入式框架。

请将项目的 info.plist 中的部署目标更新到8.0 或更高版本。

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066：无效的生成注册程序程序集： *

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067：注册器无效： *

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068：目标 framework： * 的值无效。

使用--目标框架参数传递了无效的目标框架。 请指定有效的目标框架。

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070：目标框架无效： *。 有效的目标框架是： *。

使用--目标框架参数传递了无效的目标框架。 请指定有效的目标框架。

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071：未知平台： *。 这通常表示 Xamarin 中的 bug;请使用测试用例在 http://bugzilla.xamarin.com 提交 bug 报告。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072：平台 "*" 不支持扩展。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073： Xamarin \* 不支持 \* 的部署目标（最小为 \*）。 请在项目的 info.plist 中选择一个较新的部署目标。

最小部署目标是在错误消息中指定的目标;请在项目的 info.plist 中选择一个较新的部署目标。

如果无法更新部署目标，请使用较旧版本的 Xamarin。

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074： Xamarin \* 不支持 \* 的最低部署目标（\*最大值）。 请在项目的 info.plist 中选择一个较旧的部署目标，或升级到最新版本的 Xamarin。

Xamarin 不支持将最低部署目标设置为版本高于此特定版本的 Xamarin 的版本。

请在项目的 info.plist 中选择一个较旧的最低部署目标，或升级到最新版本的 Xamarin。

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075： \* 项目的体系结构 "\*" 无效。 有效的体系结构为： \*

指定的体系结构无效。 请验证体系结构是否有效。

<a name="MT0076" />

### <a name="mt0076-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0076：未指定体系结构（使用--abi 参数）。 \* 项目需要体系结构。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0077" />

### <a name="mt0077-watchos-projects-must-be-extensions"></a>MT0077： WatchOS 项目必须为扩展。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0078" />

### <a name="mt0078-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0078：在部署目标 < 8.0 （当前为 *）的情况启用了增量生成。 这不受支持（生成的应用程序将不会在 iOS 9 上启动），因此部署目标将设置为8.0。

这是一条警告，通知已将此生成的部署目标设置为8.0，使增量生成能够正常工作。

仅当部署目标至少为8.0 时才支持增量生成（因为生成的应用程序将不会在 iOS 9 上启动）。

<a name="MT0079" />

### <a name="mt0079-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0079： Xcode \* 或更高 \* 版本中建议使用的 Xcode 版本。 当前 Xcode 版本（在 \*中 \*找到）是。

这是一条警告，告知当前版本的 Xcode 不是此版本的 Xamarin 的 Xcode 的建议版本。

请升级 Xcode 以确保最佳行为。

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080：禁用 NewRefCount，--引用计数： false，已弃用。

这是一个警告，指出禁用新引用计数（--引用计数： false）的请求已被忽略。

新的引用计数功能现在对所有项目是必需的，因此不可能再禁用。

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081：命令行参数--下载-崩溃-报告还需要--

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082：仅当未使用链接时才支持复制（--启用-复制）（--nolink）。

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083： watchOS 上不支持 Asm-仅 bitcode。 使用--bitcode：标记或--bitcode： full。

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084： Bitcode 在模拟器中不受支持。 为模拟器生成时，不要传递--bitcode。

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085：未找到对 "*" 的引用。 它将自动添加。

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086：为 TVOS 或 WatchOS 生成时，必须指定目标框架（--目标框架）。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087： Boehm GC 不支持增量生成（--fastdev）。 将禁用增量生成。

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088： GC 必须处于协作模式下的 watchOS 应用。 请删除 mtouch 的--合作基金： false 参数。

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089：为 GC 启用协作模式时，选项 "\*" 不能采用值 "\*"。

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091：此版本的 Xamarin 需要 \* SDK （随附 Xcode \*）。 升级 Xcode 以获取所需的标头文件，或将托管链接器行为设置为仅链接框架 Sdk （尝试避免新的 Api）。

Xamarin 需要来自错误消息中指定的 SDK 版本的标头文件来构建你的应用程序。 修复此错误的建议方法是升级 Xcode 以获取所需的 SDK，这将包括所有必需的标头文件。 如果安装了多个版本的 Xcode，或想要在非默认位置使用 Xcode，请确保在 IDE 的首选项中设置正确的 Xcode 位置。

可能的替代解决方案是启用托管链接器。 这会删除未使用的 API，在大多数情况下，包括缺少标头文件的新 API （或不完整）。 但是，如果你的项目使用的 API 与 Xcode 提供的 SDK 版本不同，则此操作不起作用。

最后 straw 的解决方案是使用早期版本的 Xamarin，其中一种支持你的项目所需的 SDK。

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093：找不到 "mlaunch"。

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095：无法将 Aot 文件复制到目标目录 {dest}： {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096：找不到对 Xamarin .dll 的引用。

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MT0099：内部错误 *。 请使用测试用例（ https://bugzilla.xamarin.com)）来提交 bug 报告。

当 Xamarin 中的内部一致性检查失败时，将报告此错误消息。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MT0100：程序集生成目标无效： "*"。 请使用测试用例（ https://bugzilla.xamarin.com)）来提交 bug 报告。

当 Xamarin 中的内部一致性检查失败时，将报告此错误消息。

这通常表示 Xamarin 中的 bug;请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101：程序集 "*" 在--assembly-target 参数中多次指定。

错误消息中提到的程序集在--assembly-target 参数中多次指定。 请确保每个程序集只提到一次。

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102：程序集 "\*" 和 "\*" 具有相同的目标名称（"\*"），但目标不同（"\*" 和 "*"）。

错误消息中提到的程序集具有冲突的生成目标。

例如：

```
  --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary
```

此示例尝试使用相同的 make （`MyBinary`）创建动态库和框架。

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103：静态对象 "\*" 包含多个程序集（"\*"），但是每个静态对象必须只与一个程序集相对应。

错误消息中提及的程序集全部编译为单个静态对象。 这是不允许的，必须将每个程序集编译为不同的静态对象。

例如：

```
--assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary
```

此示例尝试生成包含两个程序集（`Assembly1.dll` 和 `Assembly2.dll`）的静态对象（`MyBinary`），这是不允许的。

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105：没有为 "*" 指定程序集生成目标。

当使用 `--assembly-build-target`指定程序集生成目标时，应用程序中的每个程序集都必须分配有一个生成目标。

如果错误消息中提到的程序集未分配程序集生成目标，则会报告此错误。

有关详细信息，请参阅有关 `--assembly-build-target` 的文档。

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106：程序集生成目标名称 "\*" 无效：不允许使用字符 "\*"。

程序集生成目标名称必须是有效的文件名。

例如，这些值将触发此错误：

```
--assembly-build-target:Assembly1.dll=staticobject=my/path.o
```

由于目录分隔符字符，`my/path.o` 不是有效的文件名。

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107：程序集 "\*" 具有不同的自定义 LLVM 优化（\*），在将它们全部编译为单个二进制文件时，不允许这样做。

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108：程序集生成目标 "*" 与任何程序集都不匹配。

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109：程序集 "{0}" 是从提供的路径（提供的路径： {1}，实际路径： {2}）以外的其他路径加载的。

这是一条警告，指示应用程序引用的程序集是从不同于所请求的位置加载的。

这可能意味着应用引用的多个程序集具有相同的名称，但来自不同位置，这可能会导致意外的结果（仅使用第一个程序集）。

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110：增量生成已禁用，因为此版本的 Xamarin 版本不支持包含第三方绑定库和编译为 bitcode 的项目中的增量生成。

增量生成已禁用，因为此版本的 Xamarin 不支持包含第三方绑定库并编译为 bitcode （tvOS 和 watchOS 项目）的项目中的增量生成。

不需要执行任何操作，此消息只是信息性消息。

有关详细信息，请参阅 bug #[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710)。

此警告不再报告。

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111：已启用 Bitcode，因为此版本的 Xamarin 不支持使用 LLVM 生成 watchOS 项目，不启用 Bitcode。

已自动启用 Bitcode，因为此版本的 Xamarin 不支持使用 LLVM 生成 watchOS 项目，不启用 Bitcode。

不需要执行任何操作，此消息只是信息性消息。

有关详细信息，请参阅 bug #[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634)。

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112：本机代码共享已禁用，因为 *

有多种原因可禁用代码共享：

- 因为容器应用的部署目标早于 iOS 8.0 （即 *））。

本机代码共享需要 iOS 8.0，因为本机代码共享是使用与 iOS 8.0 一起引入的用户框架实现的。

- 由于容器应用包含国际化程序集（*）。

如果容器应用包含国际化程序集，则当前不支持本机代码共享。

- 由于容器应用具有托管链接器（*）的自定义 xml 定义。

对于使用托管链接器的自定义 xml 定义的项目，不支持本机代码共享。

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113：为扩展 "*" 禁用了本机代码共享，因为 *。

- 因为 bitcode 选项在容器应用（\*）和扩展（\*）之间有所不同。

  本机代码共享要求共享代码的项目之间的 bitcode 选项匹配。

- 由于--assembly-目标选项在容器应用（\*）和扩展（\*）之间有所不同。

  本机代码共享要求共享代码的项目之间的--assembly-target 选项相同。

  如果未在所有项目中启用或禁用增量生成，则可能出现此情况。

- 由于国际化程序集在容器应用（\*）和扩展（\*）之间有所不同。

  对于包含国际化程序集的扩展，当前不支持本机代码共享。

- 因为 AOT 编译器的参数在容器应用（\*）和扩展（\*）之间有所不同。

  本机代码共享要求 AOT 编译器的参数在共享代码的项目之间没有差异。

- 因为 AOT 编译器的其他参数在容器应用（\*）和扩展（\*）之间有所不同。

  本机代码共享要求 AOT 编译器的参数在共享代码的项目之间没有差异。

  如果两个项目之间的 "执行所有32位浮点64运算" 均不同，则会发生这种情况。

- 由于未在容器应用（\*）和扩展（\*）中启用或禁用 LLVM。

  本机代码共享要求对所有共享代码的项目启用或禁用 LLVM。

- 由于托管链接器设置在容器应用（\*）和扩展（\*）之间有所不同。

  本机代码共享要求托管链接器设置对于共享代码的所有项目都是相同的。

- 由于托管链接器的跳过程序集在容器应用（\*）和扩展（\*）之间有所不同。

  本机代码共享要求托管链接器设置对于共享代码的所有项目都是相同的。

- 因为该扩展具有托管链接器（*）的自定义 xml 定义。

  对于使用托管链接器的自定义 xml 定义的项目，不支持本机代码共享。

- 由于容器应用不是针对 ABI * （扩展是为此 ABI 构建的）而生成的。

  本机代码共享要求容器应用为所有应用扩展生成的所有体系结构生成。

  例如：在 ARM64 + ARMv7 的扩展生成时出现这种情况，但容器应用仅生成 ARM64。

- 由于容器应用正在为 ABI \*生成，因此它与扩展的 ABI （\*）不兼容。

  本机代码共享要求所有项目都为完全相同的 API 而生成。

  例如：当扩展 ARMv7 + llvm + thumb2 时，但容器应用仅构建 ARMv7 + llvm 时，会发生这种情况。

- 由于容器应用正在从 "\*" 引用程序集 "\*"，而扩展从 "*" 引用不同版本。

  本机代码共享要求所有共享代码的项目对所有程序集使用相同的版本。

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115：如果启用 bitcode，则建议使用代码（--动态符号模式 = 代码）引用动态符号。

Xamarin iOS 项目经常会动态引用本机符号，这意味着本机链接器可能会在本机链接过程中删除此类本机符号，因为本机链接器看不到使用这些符号。

通常，Xamarin 会要求本机链接器保留此类符号（使用 `-u symbol` 链接器标志），但在为 bitcode 进行编译时，本机链接器不接受 `-u` 标志。

Xamarin 已经实现了替代解决方案：我们生成引用这些符号的额外的本机代码，因此本机链接器会看到使用这些符号。 编译到 bitcode 时将自动完成此操作。

如果将 `--dynamic-symbol-mode=linker` 传递到 mtouch，则将禁用此替代解决方案，而 Xamarin 会尝试将 `-u` 传递到本机链接器。 这很可能会导致本机链接器错误。

解决方法是从项目的生成选项中的其他 mtouch 参数中移除 `--dynamic-symbol-mode=linker` 参数。

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116：结构无效： {拱}。 当部署目标为11或更高版本时，不支持32位体系结构。 请确保该项目不是针对32位体系结构生成的。

iOS 11 不包含对32位应用程序的支持，因此当部署目标为 iOS 11 或更高版本时，不支持为32位应用程序生成。

请将项目的 iOS 生成选项中的目标体系结构更改为 arm64，或将项目的 info.plist 中的部署目标更改为早期的 iOS 版本。

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117：无法在仅支持64位的模拟器上启动32位应用。

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118：在预期目录 "{msymdir}" 中找不到 Aot 文件。

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123：可执行程序集 \* 未引用 \*。

在可执行程序集中找不到平台程序集（Xamarin. TVOS/WatchOS）的任何引用。

这种情况通常发生在可执行项目中没有任何使用平台程序集的代码的情况下;例如，空 Main 方法（而不是其他代码）会显示此错误：

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

使用平台程序集中的 API 将解决此错误：

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124：无法将当前语言设置为 "{lang}" （根据 LANG = {LANG}）： {exception}

这是一条警告，指示无法将当前语言设置为错误消息中的语言。

当前语言将默认为系统语言。

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125：在模拟器中忽略--assembly-target 命令行参数。

不需要执行任何操作，此消息只是信息性消息。

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126：增量生成已禁用，因为在模拟器中不支持增量生成。

不需要执行任何操作，此消息只是信息性消息。

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127：增量生成已禁用，因为此版本的 Xamarin 版本不支持包含多个第三方绑定库的项目中的增量生成。

已自动禁用增量生成，因为此版本的 Xamarin 版本并非始终使用正确的多个第三方绑定库生成项目。

不需要执行任何操作，此消息只是信息性消息。

有关详细信息，请参阅 bug #[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727)。

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128：无法触及文件 "\*"： \*

触摸文件时出错（这是为了确保部分生成正确完成）。

最可能忽略此警告;如果出现任何问题，请在[GitHub](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题，并进行调查。

<a name="MT0135" />

### <a name="mt0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MT0135：未链接系统框架 "{0}" （由程序集 "{1}" 引用），因为它是在 {2} {3}中引入的，并且我们使用的是 {2} {4} SDK。

若要生成应用程序，Xamarin 必须与系统库链接，其中一些系统库依赖于错误消息中指定的 SDK 版本。 由于使用的是较旧版本的 SDK，对这些 Api 的调用可能会在运行时失败。

修复此错误的建议方法是升级 Xcode 以获取所需的 SDK。 如果安装了多个版本的 Xcode，或想要在非默认位置使用 Xcode，请确保在 IDE 的首选项中设置正确的 Xcode 位置。

此外，还可以让托管[链接器](https://docs.microsoft.com/xamarin/ios/deploy-test/linker)删除未使用的 api，包括（在大多数情况下）需要指定库的新 api。 但是，如果你的项目需要在比你的 Xcode 提供的 SDK 更高的 SDK 中引入的 Api，这将不起作用。

作为最后 straw 的解决方案，请使用较旧版本的 Xamarin，不需要在生成过程中提供这些新的 Sdk。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx：与项目相关的错误消息

### <a name="mt10xx-installer--mtouch"></a>MT10xx： Installer/mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001：在指定的目录中找不到应用程序

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002：无法创建符号链接，已复制文件

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003：无法终止应用程序 "*"。 可能需要手动终止应用程序。

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004：无法获取已安装应用程序的列表。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx：与项目相关的错误消息

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005：无法在设备 "\*" 上终止应用程序 "\*"： *-你可能需要手动终止应用程序。

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006：无法在设备 "\*" 上安装应用程序 "\*"： *。

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007：无法在设备 "\*" 上启动应用程序 "\*"： *。 你仍可以通过点击来手动启动应用程序。

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008：无法启动模拟器

如果 mtouch 无法启动模拟器，则会报告此错误。   有时可能会发生这种情况，因为正在运行过时或死模拟器进程。

在 Unix 命令行上发出的以下命令可用于终止停滞的模拟器进程：

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009：无法将程序集 "\*" 复制到 "\*"： *

这是某些版本的 Xamarin 中的已知问题。

如果出现这种情况，请尝试以下解决方法：

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

但是，由于此问题已在最新版本的 Xamarin 中得到解决，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用完整版本信息记录新问题并生成日志输出。

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010：无法加载程序集 "\*"： \*

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011：无法添加缺少的资源文件： "*"

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012：无法列出设备 "\*" 上的应用： \*

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013：依赖关系跟踪错误：没有要比较的文件。 请使用测试用例在 http://bugzilla.xamarin.com 提交 bug 报告。

这表示 Xamarin 中的 bug。 请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014：无法重新使用 "*" 的缓存版本： *。

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015：无法创建可执行文件 "\*"： \*

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015：无法创建可执行文件 "\*"： \*

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016：无法创建公告文件，因为已存在具有相同名称的目录。

从项目中删除 `NOTICE` 的目录。

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017：无法创建通知文件： *。

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018：应用程序的代码签名检查失败，无法在设备 "*" 上安装。 检查证书、预配配置文件和包 id。 你的设备可能不是所选预配配置文件的一部分（错误：0xe8008015）。

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019：你的应用程序具有当前预配配置文件不支持的权利，因此无法在设备 "*" 上安装。 有关更多详细信息，请查看 iOS 设备日志（错误：0xe8008016）。

在以下情况下可能发生这种问题：

- 你的应用程序具有当前预配配置文件不支持的权利。
  可能的解决方法：
  - 指定支持应用程序所需的权利的其他预配配置文件。
  - 删除当前预配配置文件中不支持的权利。
- 你正在尝试部署到的设备未包含在你所使用的预配配置文件中。
  可能的解决方法：
  - 从 Xcode 中的模板创建新应用，选择相同的配置文件，并将其部署到相同的设备。 有时，Xcode 可以通过新设备自动刷新预配配置文件（在其他情况下，Xcode 会询问你要执行的操作）。
  -前往 iOS 开发人员中心，用新设备更新配置文件，然后将更新的预配配置文件下载到计算机。

在大多数情况下，有关失败的详细信息将打印到 iOS 设备日志，这有助于诊断问题。

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020：无法在设备 "\*" 上启动应用程序 "\*"： *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021：无法将文件 "\*" 复制到 "\*"： {2}

未能复制文件。 复制操作中的错误消息包含有关错误的详细信息。

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022：无法将目录 "\*" 复制到 "\*"： {2}

无法复制目录。 复制操作中的错误消息包含有关错误的详细信息。

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023：无法与设备通信以查找应用程序 "\*"： \*

尝试在设备上查找应用程序时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024：无法在设备 "*" 上验证应用程序签名。 请确保预配配置文件已安装且未过期（错误：0xe8008017）。

由于无法验证签名，设备拒绝了应用程序安装。

请确保已安装预配配置文件，但未过期。

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025：无法在设备上列出故障报告 *。

尝试在设备上列出故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026：无法从设备 \*下载 \* 崩溃报告。

尝试从设备下载故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027：不能使用 Xcode 7 + 在使用 iOS 的设备上启动应用程序 * （Xcode 7 仅支持 iOS 6 +）。

不能使用 Xcode 7 + 来启动低于6.0 的 iOS 版本的设备上的应用程序。

请使用较旧版本的 Xcode，或手动点击应用。

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028：设备规范无效： "*"。 应为 "ios"、"watchos" 或 "all"。

使用--device 传递的设备规范无效。 有效值为： "ios"、"watchos" 或 "all"。

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029：在指定的目录中找不到应用程序： *

传递给--launchdev 的应用程序路径不存在。 请指定有效的应用程序捆绑包。

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030：不推荐使用包标识符在设备上启动应用程序。 请将完整路径传递到要启动的捆绑包。

建议将路径传递到要在设备上启动的应用，而不只是绑定 id。

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031：无法在设备 "\*" 上启动应用 "\*"，因为设备已锁定。 请解锁设备并重试。

请解锁设备并重试。

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032：此应用程序可执行文件可能太大（* MB），无法在设备上执行。 如果启用了 bitcode，则可能需要禁用它以进行开发，只需将应用程序提交到 Apple。

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033：无法从设备 "\*" 中卸载应用程序 "\*"： *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035：不能包含框架 "{name}" 的不同版本

不能与同一框架的不同版本链接。

当扩展插件引用不同版本的框架（可能通过第三方绑定程序集）时，通常会发生这种情况。

如果出现此错误，将会出现多个[MT1036](#MT1036)错误，其中列出了每个不同框架的路径。

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036：包含的框架 "{name}"： {path} （与前一个错误相关）

此错误仅与[MT1036](#MT1036)一起报告。 有关详细信息，请参阅[MT1036](#MT1036) 。

### <a name="mt11xx-debug-service"></a>MT11xx：调试服务

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101：无法启动应用

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102：无法附加到应用（以终止应用）： *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103：无法分离

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104：无法发送数据包： *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105：意外的响应类型

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106：无法获取设备上的应用程序列表：请求超时。

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107：应用程序未能启动： *

请检查你的设备是否已锁定。

如果你正在部署企业应用或使用免费预配配置文件，你可能会相信开发人员（<a href="https://stackoverflow.com/a/30726375/183422">此处</a>对此进行了说明）。

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108：找不到此 XX （YY）设备的开发人员工具。

Mtouch 中的一些操作需要 `DeveloperDiskImage.dmg` 文件存在。   此文件是 Xcode 的一部分，通常与用于构建的 SDK 相对应，在 `Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg`中。

发生此错误的原因可能是您没有与您连接的设备相匹配的 DeveloperDiskImage。

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109：应用程序无法启动，因为设备已锁定。 请解锁设备并重试。

请检查你的设备是否已锁定。

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110：由于 iOS 安全限制，无法启动应用程序。 请确保开发人员受信任。

如果你正在部署企业应用或使用免费预配配置文件，你可能会相信开发人员（<a href="https://stackoverflow.com/a/30726375/183422">此处</a>对此进行了说明）。

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111：应用程序已成功启动，但无法等待应用按要求退出，因为使用 gdbserver 启动时无法检测到应用终止。

### <a name="mt12xx-simulator"></a>MT12xx：模拟器

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201：无法加载模拟器： *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202：无效的模拟器配置： *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203：无效的模拟器规范： *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204：无效的模拟器规范 "*"：未指定运行时。

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205：无效的模拟器规范 "*"：未指定设备类型。

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206：找不到模拟器运行时 "*"。

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207：找不到模拟器设备类型 "*"。

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208：找不到模拟器运行时 "*"。

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209：找不到模拟器设备类型 "*"。

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210：无效的模拟器规范： \*，未知密钥 "\*"

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211：模拟器版本 "\*" 不支持模拟器类型 "\*"

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212：无法创建类型为 \* 且运行时 = \*的模拟器版本。

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213： Xcode 4 的模拟器规范无效： *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214： Xcode 5 的模拟器规范无效： *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215：指定的 SDK 无效： *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216：找不到模拟器 UDID "*"。

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217：无法加载 "*" 处的应用程序捆绑包。

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218：在 "*" 的应用中找不到任何捆绑标识符。

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219：找不到 "*" 的模拟器。

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220：找不到设备 "*" 的最新模拟器运行时。

这通常表示 Xcode 存在问题。

若要尝试修复此问题，请执行以下操作：

- 在 Xcode 中使用一次模拟器。
- 使用--sdk \<版本 > 传递显式 SDK 版本。
- 重新安装 Xcode。

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221：无法为 WatchOS 模拟器 "*" 找到配对的 iPhone 模拟器。

在 WatchOS 模拟器中启动 WatchOS 应用程序时，还必须有配对的 iOS 模拟器。

可以使用 Xcode 的设备 UI （菜单窗口-> 设备）将手表模拟器与 iOS 模拟器配对。

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301：已忽略本机库 `*` （\*），因为它与当前生成体系结构（\*）不匹配

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302：无法从 "+" 提取本机库 "*"。 请确保本机库已正确嵌入托管程序集中（如果程序集是使用绑定项目生成的，则必须在项目中包括本机库，并且其生成操作必须为 "ObjcBindingNativeLibrary"）。

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303：无法从 "\*" 解压缩本机框架 "\*"。 请查看生成日志以了解有关本机 "zip" 命令的详细信息。

无法从绑定库中解压缩指定的本机框架。

有关此错误的详细信息，请查看 bulid 日志。

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304： \* 中的嵌入框架 "\*" 无效：它不包含 info.plist。

指定的嵌入框架不包含 info.plist，因此不是有效的框架。

请确保此框架有效。

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305：绑定库 "\*" 包含用户框架（\*），但嵌入的用户框架需要 iOS 8.0 （当前部署目标为 *）。 请在 info.plist 文件中将部署目标设置为至少8.0。

指定的绑定库包含一个嵌入框架，但 Xamarin 仅支持 iOS 8.0 或更高版本上的嵌入式框架。

请在 info.plist 文件中将部署目标设置为至少8.0，以解决此错误（或不使用嵌入的框架）。

### <a name="mt14xx-crash-reports"></a>MT14xx：崩溃报告

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400：无法打开故障报告服务： AFCConnectionOpen 返回 *

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401：无法关闭故障报告服务：返回了 AFCConnectionClose *

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402：无法读取 \*： AFCFileInfoOpen 返回的文件信息 \*

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403：无法读取故障报告：返回了 AFCDirectoryOpen （\*）： \*

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404：无法读取故障报告：返回了 AFCFileRefOpen （\*）： \*

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405：无法读取故障报告：返回了 AFCFileRefRead （\*）： \*

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406：无法列出故障报告：返回了 AFCDirectoryOpen （\*）： \*

尝试从设备访问故障报告时出错。

要解决此问题，请执行以下操作：

- 请从设备中删除应用程序，然后重试。
- 断开并重新连接设备。
- 重新启动设备。
- 重新启动 Mac。
- 将设备与 iTunes 同步（这将从设备中删除任何故障报告）。

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600：不是 Mach-o-O 动态库（未知标头 "0x *"）： *。

处理相关动态库时出错。

请确保动态库是有效的 Mach-o-O 动态库。

可以使用终端中的 `file` 命令验证库的格式：

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601：不是静态库（未知标头 "*"）： *。

处理相关静态库时出错。

请确保静态库是有效的 Mach-o-O 静态库。

可以使用终端中的 `file` 命令验证库的格式：

```
file -arch all -l /path/to/library.a
```

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602：不是 Mach-o-O 动态库（未知标头 "0x *"）： *。

处理相关动态库时出错。

请确保动态库是有效的 Mach-o-O 动态库。

可以使用终端中的 `file` 命令验证库的格式：

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603： \*中位置 \* 的 fat 条目的格式未知。

处理相关 fat 存档时出错。

请确保 fat 存档是有效的。

可以使用终端中的 `file` 命令验证 fat 存档格式：

```
file -arch all -l /path/to/file
```

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604：类型 \* 的文件不是 MachO 文件（\*）。

处理相关的 MachO 文件时出错。

请确保该文件是有效的 Mach-o-O 动态库。

可以使用终端中的 `file` 命令来验证文件的格式：

```
file -arch all -l /path/to/file
```

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx：链接器错误消息

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001：无法链接程序集

此错误表示托管链接器遇到意外错误，如异常，无法完成或保存正在处理的程序集。 有关确切错误的详细信息将是生成日志的一部分，例如

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

对于此类问题，请务必提交 bug 报告。 在大多数情况下，可以在发布适当的修补程序之前提供一种解决方法。 若要解决此问题，上述信息是关键的（以及一个测试用例和/或程序集 binairy）。

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002：无法解析引用： *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003：将忽略选项 "*"，因为链接已禁用

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004：找不到额外的链接器定义文件 "*"。

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005：无法分析 "*" 中的定义。

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006：无法从以下内容加载 mscorlib.dll： *。 请重新安装 Xamarin。

这通常表示你的 Xamarin iOS 安装有问题。 请尝试重新安装 Xamarin。

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010：未知的 HttpMessageHandler `*`。 有效值为 HttpClientHandler （默认值）、CFNetworkHandler 或 NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011：未知的 TlsProvider `*`。  有效值为 default 或 appletls。

为 `tls-provider=` 提供的值不是有效的 TLS （传输层安全性）提供程序。

`default` 和 `appletls` 是唯一有效的值，并且两者都表示相同的选项，这是使用本机 Apple TLS API 提供 SSL/TLS 支持。

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015： watchOS 的 HttpMessageHandler `*` 无效。 唯一有效的值为 NSUrlSessionHandler。

出现此警告是因为项目文件指定了无效的 HttpMessageHandler。

默认情况下，生成的预览工具的早期版本在项目文件中生成的值无效。

若要修复此警告，请在文本编辑器中打开项目文件，然后从 XML 中删除所有 HttpMessageHandler 节点。

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016：无效的 TlsProvider `legacy` 选项。 将使用唯一的有效值 `appletls`。

`legacy` 提供程序，它是完全托管的 SSLv3/TLSv1 唯一的提供程序，不再随 Xamarin 一起提供。 使用此旧提供程序并现在使用较新的 `appletls` 生成的项目。

若要修复此警告，请在文本编辑器中打开项目文件，然后从 XML 中删除所有 "MtouchTlsProvider" 节点。

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017：无法处理 XML 说明。

这意味着你提供的[自定义 XML 链接器配置文件](~/cross-platform/deploy-test/linker.md)中存在错误，请查看你的文件。

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018：程序集 "\*" 从两个不同的位置引用： "\*" 和 "*"。

错误消息中提到的程序集从多个位置加载。 请确保始终使用同一版本的程序集。

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019：无法加载根程序集 "*"

未能加载根程序集。 请验证错误消息中的路径是否引用了现有文件，以及该文件是否为有效的 .NET 程序集。

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x：绑定优化器未能处理 `...`。

尝试优化生成的绑定代码时出现意外情况。 错误消息中命名了导致问题的元素。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供名为（或包含名为的类型或方法）的程序集，并提供启用了详细级别（即 `-v -v -v -v` 在**附加的 mtouch 参数**中）的完整生成日志。

最后一个数字 `x` 为：

- 程序集名称的 `0`;
- 类型名称的 `1`;
- 方法名称的 `3`;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030：删除 `...`处理失败的用户资源。

尝试删除用户资源时出现意外情况。 错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

用户资源是在生成时需要提取的程序集（作为资源）中包含的文件，用于创建应用程序捆绑包。 这包括：

- `__monotouch_content_*` 和 `__monotouch_pages_*` 资源;与
- 嵌入到绑定程序集内的本机库;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040：默认 HttpMessageHandler setter 处理 `...`失败。

尝试设置应用程序的默认 `HttpMessageHandler` 时出现意外情况。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上记录新问题，并在启用详细级别的情况（即，`-v -v -v -v` 在附加的**mtouch 参数**中）创建完整的生成日志。

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050：代码 Remover 处理 `...`失败。

尝试从应用程序的 BCL 装运中移除代码时出现意外情况。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上记录新问题，并在启用详细级别的情况（即，`-v -v -v -v` 在附加的**mtouch 参数**中）创建完整的生成日志。

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060： Managementpack 处理 `...`失败。

尝试密封类型或方法（final）或 devirtualizing 某些方法时出现意外情况。 错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070：元数据化简器处理 `...`失败。

尝试从应用程序中减少元数据时出现意外情况。 错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080： MarkNSObjects 处理 `...`失败。

尝试从应用程序中标记 `NSObject` 子类时出现意外情况。 错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090：内联方处理 `...`失败。

尝试从应用程序内联代码时出现意外情况。 错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100：智能枚举转换保留处理 `...`失败。

尝试从应用程序中为智能枚举标记转换方法时出现意外情况。 错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101：无法解析引用 "\*"，它是从 "*" 中的方法 "\*" 引用的。

处理错误消息中提到的方法时遇到无效的程序集引用。

错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102：处理程序集 "\*" 中的方法 "\*" 时出错： *

尝试标记错误消息中提到的方法时出现意外情况。

错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103：处理程序集 "\*" 时出错： *

处理程序集时出现意外错误。

错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新问题中提供程序集，并在启用了详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104：无法将程序集 "{0}" 链接为混合模式。

链接器无法处理混合模式程序集。

有关混合模式程序集的详细信息，请参阅 https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies。

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx： AOT 错误消息

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001：无法对程序集 "*" 执行 AOT 操作

这通常指示 AOT 编译器中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提供一个新问题，其中包含可用于再现错误的项目。

有时，可以通过在项目的 "iOS 生成" 选项中禁用增量生成来解决此问题（但仍是一个 bug，因此请务必报告）。

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-developerxamarincomguidesiosadvanced_topicslimitationsreverse_callbacks"></a>MT3002： AOT 限制：方法 "*" 必须是静态的，因为它是用 [MonoPInvokeCallback] 修饰的。 请参阅[developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](~/ios/internals/limitations.md#reverse-callbacks)

此错误消息来自 AOT 编译器。

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003：冲突--debug 和--llvm 选项。 已禁用软调试。

启用 LLVM 时，不支持调试。 如果需要调试应用，请先禁用 LLVM。

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004：无法对程序集 "*" 执行 AOT 操作，因为该程序集不存在。

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005：找不到程序集 "\*" 的依赖项 "\*"。 请查看项目的引用。

当程序集引用平台程序集的另一个版本（通常是 mscorlib.dll 的 .NET 4 版本）时，通常会发生这种情况。

这不受支持，可能无法正确生成或执行（该程序集可能使用 Xamarin 版本所没有的 mscorlib.dll 的 .NET 4 版本中的 API）。

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006：无法计算项目的完整依赖关系映射。 这将导致生成时间变慢，因为 Xamarin 无法正确检测需要重新生成的内容（不需要重新生成哪些内容）。 有关更多详细信息，请查看前面的警告。

 生成或正确执行（程序集可能使用 Xamarin 版本的 .NET 4 版本的 mscorlib.dll 中没有的 API）。

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007：启用 llvm 后，不会加载调试信息文件（* .mdb）。

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008： Bitcode 支持需要使用 LLVM AOT 后端（--LLVM）

Bitcode 支持需要使用 LLVM AOT 后端（--LLVM）。

禁用 Bitcode 支持或启用 LLVM。

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx：代码生成错误消息

### <a name="mt40xx-main"></a>MT40xx： Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001：无法将主模板展开为 `*`。

生成 `main.m`时出错。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002：无法为 P/Invoke 方法编译生成的代码。 请在 http://bugzilla.xamarin.com 提交 bug 报告

未能编译 P/Invoke 方法的生成代码。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

### <a name="mt41xx-registrar"></a>MT41xx：注册机构

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101：注册器无法为类型 `*`生成签名。

在导出的 API 中找到了一个类型，运行时不知道如何封送到目标-C。

如果你认为 Xamarin 应支持相关类型，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增强请求。

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102：注册器在 `*`方法的签名中发现无效类型 `*`。 请改用 `*`。

这种情况目前仅在一种类型： System.web 中发生。 改为使用 NSDate 等效项（）。

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103：注册器在方法 `*`的签名中发现无效的类型 `*`：该类型实现了 INativeObject，但没有采用两个（IntPtr，bool）参数的构造函数

当注册机构在具有所述特性的签名中遇到类型时，会发生这种情况。 注册机构可能需要创建类型的新实例，在这种情况下，它需要具有（IntPtr，bool）签名的构造函数-第一个参数（IntPtr）指定托管句柄，第二个参数（IntPtr）用于指定托管句柄。句柄（如果此值为 false，则将对对象调用 "retain"）。

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104：注册器无法封送 `*`方法的签名中类型 `*` 的返回值。

在导出的 API 中找到了一个类型，运行时不知道如何封送到目标-C。

如果你认为 Xamarin 应支持相关类型，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增强请求。

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105：注册器无法封送 `*`方法的签名中 `*` 类型的参数。

如果你认为 Xamarin 应支持相关类型，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增强请求。

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106：注册器无法封送 `*`方法的签名中结构 `*` 的返回值。

在导出的 API 中找到了一个类型，运行时不知道如何封送到目标-C。

如果你认为 Xamarin 应支持相关类型，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增强请求。

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107：注册器无法封送 `+`方法的签名中 `*` 类型的参数。

在导出的 API 中找到了一个类型，运行时不知道如何封送到目标-C。

如果你认为 Xamarin 应支持相关类型，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增强请求。

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108：注册器无法获取托管类型 `*`的 ObjectiveC 类型。

在导出的 API 中找到了一个类型，运行时不知道如何封送到目标-C。

如果你认为 Xamarin 应支持相关类型，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增强请求。

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109：无法编译生成的注册器代码。 请在 http://bugzilla.xamarin.com 提交 bug 报告

未能编译注册器的生成代码。 生成日志将包含本机编译器的输出，说明代码不编译的原因。

这始终是 Xamarin 中的 bug;请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布项目或测试用例的新问题。

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110：注册器无法封送 `*`方法的签名中 `*` 类型的 out 参数。

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111：注册器无法在方法 `*`中为类型 `*` 生成签名。

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvanced_topicsregistrar-for-more-information"></a>MT4112：注册器发现 `*`的无效类型。 不支持使用目标-C 注册泛型类型，并且可能会导致随机行为和/或崩溃（以便与早期版本的 Xamarin 向后兼容。 iOS 可以通过将 `--unsupported--enable-generics-in-registrar` 作为附加的 mtouch 参数传递到项目的 "iOS 生成" 选项页来忽略此错误。 有关详细信息，请参阅[developer.xamarin.com/guides/ios/advanced_topics/registrar](~/ios/internals/registrar.md) 。

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113：注册器找到了一个泛型方法： "\*。\*"。 不支持导出泛型方法，这会导致随机行为和/或崩溃。

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114：方法 "\*的注册器中出现意外错误。\*"-请在 http://bugzilla.xamarin.com 提交 bug 报告

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116：无法注册程序集 "\*"： \*

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117：注册机构发现方法 "\*中的签名不匹配。\*"-选择器指示方法采用 \* 参数，而托管方法具有 \* 参数。

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118：无法注册具有相同本地名称（"*"）的两个托管类型（"\*" 和 "\*"）。

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119：无法注册成员 "\*. *" 的选择器 "\*"，因为该选择器已在另一个成员上注册。

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120：注册器在字段 "\*. *" 中找到未知字段类型 "\*"。 请在 http://bugzilla.xamarin.com 提交 bug 报告

此错误表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121：使用帐户框架时，无法使用 GCC/G + + 从静态注册器编译生成的代码（在编译过程中使用的 Apple 提供的标头文件需要 Clang）。 请使用 Clang （--编译器： Clang）或动态注册机构（--注册员： dynamic）。

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122：不能使用中提供的 Clang 编译器 *。* 在应用程序中存在非 ASCII 类型名称（"*"）时，用于从静态注册器编译生成的代码的 SDK。 使用 GCC/G + + （--编译器： GCC | G + +）、动态注册机构（--注册员：动态）或更高版本的 SDK。

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123：可变参数函数 "*" 中可变参数参数的类型必须为。

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124：在 "\*" 上找到的 \* 无效。 请在 http://bugzilla.xamarin.com 提交 bug 报告

此错误表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125：注册机构在 "\*" 方法的签名中找到无效类型 "\*"：接口必须具有指定其包装类型的协议属性。

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126：无法注册具有相同本地名称（"*"）的两个托管协议（"\*" 和 "\*"）。

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127：无法为方法 "\*" （实现 "\*"）注册多个接口方法。

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128：注册器在方法 "\*" 中找到无效的泛型参数类型 "\*"。 泛型参数必须具有 "NSObject" 约束。

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129：注册器在方法 "\*" 中找到无效的泛型返回类型 "\*"。 泛型返回类型必须具有 "NSObject" 约束。

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130：注册器无法导出泛型类（"*"）中的静态方法。

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131：注册器无法导出泛型类中的静态属性（"\*\*"）。

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132：注册机构在属性 "\*" 中找到无效的泛型返回类型 "\*"。 返回类型必须具有 "NSObject" 约束。

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133：无法构造目标为 C 的类型 "*" 的实例，因为该类型为泛型。 [运行时异常]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134：你的应用程序使用的是 "\*" 框架，它不包含在用于构建应用的 iOS SDK 中（此框架是在 iOS \*中引入的，而你是使用 iOS \* SDK 生成的。）请在应用的 iOS 生成选项中选择一个较新的 SDK。

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135：成员 "\*。\*"具有未指定选择器的导出特性。 选择器是必需的。

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136：注册器无法封送方法 "\*. *" 中参数 "\*" 的参数类型 "\*"

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138：注册器无法封送属性 "\*. *" 的属性类型 "\*"。

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139：注册器无法封送属性 "\*. *" 的属性类型 "\*"。 具有 [Connect] 特性的属性的属性类型必须为 NSObject （或 NSObject 的子类）。

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140：注册机构发现方法 "\*中的签名不匹配。\*"-选择器指示可变参数方法采用 \* 参数，而托管方法具有 \* 参数。

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141：无法在成员 "\*" 上注册选择器 "\*"，因为 Xamarin 会隐式注册此选择器。

这会在为框架类型建立子类并尝试实现 "retain"、"release" 或 "dealloc" 方法时发生：

```csharp
class MyNSObject : NSObject
{
  [Export ("retain")]
  new void Retain () {}

  [Export ("release")]
  new void Release () {}

  [Export ("dealloc")]
  new void Dealloc () {}
}
```

但如果类不是框架类型的第一个子类，则可以重写这些方法：

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
  [Export ("retain")]
  new void Retain () {}

  [Export ("release")]
  new void Release () {}

  [Export ("dealloc")]
  new void Dealloc () {}
}
```

在这种情况下，Xamarin 将覆盖 `MyNSObject` 类 `retain`、`release` 和 `dealloc`，并且不存在冲突。

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142：无法注册类型 "*"。

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143：无法注册 ObjectiveC 类 "*"，它似乎不是从任何已知 ObjectiveC 类（包括 NSObject）派生的。

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144：无法注册方法 "*"，因为它没有关联的 trampoline。 请在 http://bugzilla.xamarin.com提交 bug 报告。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145：枚举 "*" 无效：包含 [Native] 特性的枚举必须具有 "long" 或 "ulong" 的基础枚举类型。

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146：类 "\*" （"\*"）上的注册器特性的 Name 参数包含无效字符： "\*" （\*）。

Objectice 类的名称不能包含空格，这意味着，相应托管类的 `Register` 属性不能有 `Name` 参数不能包含空格。

请验证错误消息中提及的托管类的 `Register` 属性是否不包含任何空格。

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147：检测到在使用动态注册器时从 JSExport 协议继承的协议。 不能将协议动态导出到 JavaScriptCore;必须使用静态注册器（将 "--注册员： static" 添加到项目的 iOS 生成选项中的其他 mtouch 参数，以选择静态注册器）。

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148：注册机构找到了一般协议： "*"。 不支持导出通用协议。

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149：无法注册方法 "\*。\*"，因为第一个参数的类型（"\*"）与类别类型（"\*"）不匹配。

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150：无法注册类型 "\*"，因为其类别特性中的类型属性（"\*"）未从 NSObject 继承。

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151：无法注册类型 "*"，因为未设置其 Category 特性中的 Type 属性。

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152：无法将类型 "*" 注册为类别，因为它实现 INativeObject 或子类 NSObject。

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153：无法将类型 "\*" 注册为类别，因为它是泛型的。

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154：无法将方法 "\*" 注册为 category 方法，因为该方法是泛型方法。

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155：无法使用选择器 "\*" 将方法 "\*" 注册为 "*" 上的类别方法，因为目标 C 已经具有此选择器的实现。

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156：无法注册具有相同本地名称（"*"）的两个类别（"\*" 和 "\*"）。

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157：无法注册 category 方法 "\*"，因为至少需要一个参数（并且其类型必须与类别类型 "\*" 匹配）

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158：无法在类别 \* 中注册 \* 构造函数，因为不支持类别中的构造函数。

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159：无法将方法 "*" 注册为 category 方法，因为类别方法必须是静态的。

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160：在 "\*" 的选择器 "\*" 中找到了无效的字符 "\*" （\*）。

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161：注册器找到了不受支持的结构 "\*"：结构中的所有字段也必须是结构（类型为 "{2}" 的字段 "\*" 不是结构）。

注册机构找到了包含不受支持字段的结构。

结构中公开给目标 C 的所有字段也必须是结构（而不是类）。

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162：类型 "\*" （用于 * {2}）在 * * （在 * * 中引入）中不可用\* 请使用较新的 * SDK 进行构建（通常使用最新版本的 Xcode 来实现）。

注册器找到当前 SDK 中未包含的类型。

请升级 Xcode。

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163：注册机构中出现内部错误（*）。 请在 http://bugzilla.xamarin.com 提交 bug 报告

此错误表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164：无法导出属性 "\*"，因为其选择器 "\*" 是目标 C 关键字。 请使用其他名称。

相关属性的选择器不是有效的目标 C 标识符。

请使用有效的目标 C 标识符作为选择器。

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165：注册器无法在任何被引用的程序集中找到类型 "system.string"。

此错误很可能表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166：无法注册方法 "\*"，因为签名包含不是引用类型的类型（\*）。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167：无法注册方法 "\*"，因为签名包含泛型类型（\*），该类型的泛型参数类型不是 NSObject 子类（*）。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managed_name-because-its-objective-c-name-exported_name-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168：无法注册类型 "{managed\_name}"，因为其目标 C 名称 "{导出\_name}" 是一个目标 C 关键字。 请使用其他名称。

相关类型的目标-C 名称不是有效的目标 C 标识符。

请使用有效的目标 C 标识符。

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169：无法为 {method} 生成 P/Invoke 包装： {message}

对于提到的，Xamarin 未能生成 P/Invoke 包装器函数。
请检查报告的错误消息以了解根本原因。

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170：对于方法 {method} 中的返回值，注册程序无法从 "{managed type}" 转换为 "{native type}"。

请参阅错误<a href="#MT4172">MT4172</a>的说明。

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171：成员 {member} 上的 BindAs 属性无效： BindAs 类型 {type} 不同于属性类型 {type}。

请确保 BindAs 属性中的类型与它所附加到的成员的类型相匹配。

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172：注册器无法在方法 {method} 中将参数 "{parameter name}" 从 "{native type}" 转换为 "{managed type}"。

注册器不支持在所述的类型之间进行转换。

如果 Xamarin 提供了相关 API，则这是 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

如果在开发本机库的绑定项目时遇到这种情况，我们将打开添加对类型的新组合的支持。 如果是这种情况，请使用测试用例在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提供增强请求，我们将对其进行评估。

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx： GCC 和工具链错误消息

### <a name="mt51xx-compilation"></a>MT51xx：编译

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101：缺少 "*" 编译器。 请安装 Xcode "命令行工具" 组件

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102：未能组合文件 "*"。 请在 http://bugzilla.xamarin.com 提交 bug 报告

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103：无法编译文件 "*"。 请在 http://bugzilla.xamarin.com 提交 bug 报告

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104：找不到 "\*" 和 "\*" 编译器。 请安装 Xcode "命令行工具" 组件

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106：无法编译文件 "*"。 请在 http://bugzilla.xamarin.com 提交 bug 报告

这通常表示 Xamarin 中的 bug;请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

### <a name="mt52xx-linking"></a>MT52xx：链接

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201：本机链接失败。 请查看提供给 gcc 的生成日志和用户标志： *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202：本机链接失败。 请查看生成日志。

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203：本机链接警告： *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209：本机链接错误： *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210：本机链接失败，未定义的符号： *。 请验证是否已引用所有必需的框架，并正确链接了本机库。

如果本机链接器找不到某个位置引用的符号，则会发生这种情况。 可能出现这种情况的原因如下：

- 第三方绑定需要框架，但绑定不会在其 `[LinkWith]` 特性中指定此项。 解决方案：
  - 如果你是第三方绑定的作者，或有权访问其源，请修改该绑定的 `[LinkWith]` 属性，使其包含所需的框架：

    ```csharp
    [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]
    ```

  - 如果无法修改第三方绑定，可以通过将 `-gcc_flags '-framework SystemFramework'` 传递到 `mtouch` 来手动链接所需的框架（这是通过在项目的 "iOS 生成" 选项页中修改其他 mtouch 参数来完成的。 请记住，必须针对每个项目配置完成此操作）。
- 在某些情况下，托管绑定由多个本机库组成，它们必须包含在绑定中。 每个绑定项目中可以有多个本机库，因此解决方案只是将所有必需的本机库添加到绑定项目。</li>
- 托管绑定引用本地库中不存在的本机符号。
    当绑定已存在一段时间，并且在该时间段内修改了本机代码以便在绑定尚未更新的情况下删除或重命名了特定的本机类时，通常会发生这种情况。
- P/Invoke 引用了不存在的本机符号。 从 Xamarin 7.4 开始，将在此情况下报告<a href="#MT5214">MT5214</a>错误（有关详细信息，请参阅 MT5214）。
- 第三方绑定/库是使用C++生成的，但是绑定不在其 `[LinkWith]` 特性中指定此项。 这通常相当容易识别，因为符号有损坏C++的符号（一个常见示例是 `__ZNKSt9exception4whatEv`）。
  - 如果你是第三方绑定的作者，或有权访问其源，请修改该绑定的 `[LinkWith]` 属性以设置 `IsCxx` 标志：

    ```csharp
    [LinkWith ("mylib.a", IsCxx = true)]
    ```

  - 如果无法修改第三方绑定，或者是使用第三方库手动链接，则可以通过将 `-cxx` 传递到 mtouch 来设置等效标志（这是通过在项目的 "iOS 生成" 选项页中修改其他 mtouch 参数来完成的。 请记住，必须针对每个项目配置完成此操作）。

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211：本机链接失败，未定义的目标-C 类： \*。 在与应用程序链接的任何库或框架中都找不到符号 "\*"。

当本机链接器找不到某个位置引用的目标-C 类时，会发生这种情况。 出现这种情况的原因有以下几种：与用于[MT5210](#MT5210)和此外，还包括：

- 第三方绑定绑定了目标 C 协议，但没有使用其 api 定义中的 `[Protocol]` 属性对其进行批注。 解决方案：
  - 添加缺少的 `[Protocol]` 属性：

    ```csharp
    [BaseType (typeof (NSObject))]
    [Protocol] // Add this
    public interface MyProtocol
    {
    }
    ```

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212：本机链接失败，重复符号： *。

当本机链接器遇到所有本机库之间的重复符号时，会发生这种情况。 如果出现此错误，则可能存在一个或多个[MT5213](#MT5213)错误，其中包含每个符号出现的位置。 此错误的可能原因：

- 相同的本机库包含两次。
- 发生两个不同的本机库来定义相同的符号。
- 本机库未正确生成，并且包含相同的符号。
  你可以通过使用终端中的以下命令集来确认这一点（将 i386 替换为要为其构建的体系结构 x86_64/armv7/armv7s/arm64）：

  ```
  # Native libraries are usually fat libraries, containing binary code for
  # several architectures in the same file. First we extract the binary
  # code for the architecture we're interested in.
  lipo libNative.a -thin i386 -output libNative.i386.a

  # Now query the native library for the duplicated symbol.
  nm libNative.i386.a | fgrep 'SYMBOL'

  # You can also list the object files inside the native library.
  # In most cases this will reveal duplicated object files.
  ar -t libNative.i386.a
  ```

  有几种可能的解决方法：

  - 请求本机库的提供程序将其修复，并提供更新的版本。
  - 通过删除额外的对象文件来自行修复此问题（这仅适用于问题是重复的对象文件）

  ```
  # Find out if the library is a fat library, and which
  # architectures it contains.
  lipo -info libNative.a

  # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
  lipo libNative.a -thin ARCH -output libNative.ARCH.a

  # Extract the object files for the offending architecture
  # This will remove the duplicates by overwriting them
  # (since they have the same filename)
  mkdir -p ARCH
  cd ARCH
  ar -x ../libNative.ARCH.a

  # Reassemble the object files in an .a
  ar -r ../libNative.ARCH.a *.o
  cd ..

  # Reassemble the fat library
  lipo *.a -create -output libNative.a
  ```

  - 请求链接器删除未使用的代码。 如果满足以下所有条件，则 Xamarin 将自动执行此操作：
    - 所有第三方绑定的 `[LinkWith]` 属性都已启用 SmartLink：

      ```csharp
      [assembly: LinkWith ("libNative.a", SmartLink = true)]
      ```

    - 没有 `-gcc_flags` 传递到 mtouch （在项目的 iOS 生成选项的 "其他 mtouch 参数" 字段中）。
    - 还可以通过将 `-gcc_flags -dead_strip` 添加到项目的 iOS 生成选项中的其他 mtouch 参数，直接请求链接器删除未使用的代码。

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213：中的重复符号： * （与前一个错误相关的位置）

此错误仅与[MT5212](#MT5212)一起报告。 有关详细信息，请参阅[MT5212](#MT5212) 。

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214：本机链接失败，未定义的符号： *。 此符号引用了托管成员 *。 请验证是否引用了所有必需的框架，并链接了本机库。

当托管代码包含对不存在的本机方法的 P/Invoke 时，会报告此错误。 例如：

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

有几种可能的解决方案：

- 从源代码中删除有问题的 P/Invoke。
- 为所有程序集启用托管链接器（可通过将 "链接器行为" 设置为 "所有程序集"，在项目的 iOS 生成选项中完成此操作）。 这会有效地从应用中删除所有不使用的 P/Invoke （自动，而不是像以前一样手动）。 缺点是，这会使模拟器生成稍慢一些，并且可能会在应用程序使用反射时中断应用程序-有关链接器的详细信息，请参阅[此处](~/ios/deploy-test/linker.md)）
- 创建另一个包含缺少的本机符号的本机库。 请注意，这只是一种解决方法（如果您尝试调用这些功能，您的应用程序将崩溃）。

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215：对 "*" 的引用可能需要将其他 framework = XXX 或-lXXX 指令添加到本机链接器

这是一条警告，指示检测到 P/Invoke 引用相关库，但应用未与其链接。

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216： * 的本机链接失败。 请在 http://bugzilla.xamarin.com 提交 bug 报告

当链接 AOT 编译器的输出时，会报告此错误。

此错误很可能表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217：本机链接可能失败，因为链接器命令行过长（* 个字符）。

本机链接失败，因此可能发生此错误，因为链接器命令太长。

Xamarin iOS 项目经常会动态引用本机符号，这意味着本机链接器可能会在本机链接过程中删除此类本机符号，因为本机链接器看不到使用这些符号。

通常，Xamarin 会要求本机链接器使用 `-u symbol` 链接器标志来保存此类符号，但是，如果有许多这样的符号，则整个命令行可能会超出操作系统指定的最大命令行长度。

此类动态符号有几个可能的源：

- P/Invoke 到静态链接的库中的方法（其中 dll 名称在 DllImport 特性中 `__Internal` `[DllImport ("__Internal")]`）。
- 从绑定项目（`[Field]` 属性）对静态链接库中的内存位置的字段引用。
- 从绑定项目（使用增量生成时或者在不使用静态注册器时）从静态链接库引用的目标-C 类。

可能的解决方法：

- 启用托管链接器（如果可能，为所有程序集，而不是仅 SDK 程序集）。 这可能会删除动态符号的足够源，以便链接器的命令行不超过最大值。
- 减少 P/Invoke、字段引用和/或目标-C 类的数目。
- 重写动态符号以获得较短的名称。
- 在项目的 iOS 生成选项中将 `-dlsym:false` 作为附加的 mtouch 参数进行传递。 使用此选项时，Xamarin 会在 AOT 编译的代码中生成本机引用，而无需请求链接器来保留此符号。 但是，这仅适用于设备生成，如果存在对静态库中不存在的函数的 P/Invoke，则会导致链接器错误。
- 在项目的 iOS 生成选项中将 `--dynamic-symbol-mode=code` 作为附加的 mtouch 参数进行传递。 使用此选项时，Xamarin 将生成其他引用这些符号的本机代码，而不是要求本机链接器使用命令行参数保留这些符号。 此方法的缺点是，它会稍微增加可执行文件的大小。
- 通过在项目的 iOS 生成选项中将 `--registrar:static` 作为附加的 mtouch 参数进行传递来启用静态注册器（对于模拟器生成，因为静态注册器已是设备生成的默认值）。 静态注册器将生成静态引用目标 C 类的代码，因此，无需请求本机链接器来保留此类。
- 禁用增量生成（适用于设备生成）。 启用增量生成时，本机链接器不会考虑静态注册器生成的代码，这意味着 Xamarin 仍必须要求链接器保留引用的目标-C 类。 因此禁用增量生成会阻止此需求。

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218：无法忽略动态符号 {symbol} （--ignore-symbol = {symbol}），因为它不是作为动态符号检测到的。

已传递 `--ignore-dynamic-symbol=symbol` 的命令行参数，但此符号不是被识别为必须手动保留的动态符号的符号。

此操作主要有两个原因：

- 符号名称不正确。
  - 不要在符号名前面加上一个下划线。
  - 目标-C 类的符号是 `OBJC_CLASS_$_<classname>`的。
- 符号是正确的，但它是已按正常方式保留的符号（某些生成选项会导致动态符号的确切列表改变）。

### <a name="mt53xx-other-tools"></a>MT53xx：其他工具

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301：缺少 "条纹" 工具。 请安装 Xcode "命令行工具" 组件

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302：缺少 "dsymutil" 工具。 请安装 Xcode "命令行工具" 组件

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303：无法生成调试符号（dSYM 目录）。 请查看生成日志。

在应用程序目录中运行 dsymutil 以创建调试符号时出错。 请查看生成日志以查看 dsymutil 的输出，并查看如何修复它。

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304：无法去除最终的二进制文件。 请查看生成日志。

运行 "条带" 工具从应用程序中删除调试信息时出错。

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305：缺少 "lipo" 工具。 请安装 Xcode "命令行工具" 组件

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306：无法创建 fat 库。 请查看生成日志。

运行 "lipo" 工具时出错。 请查看生成日志，查看 "lipo" 报告的错误。

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307：无法对可执行文件进行签名。 请查看生成日志。

对应用程序进行签名时出错。 请查看生成日志，查看 "codesign" 报告的错误。

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx： mtouch 内部工具错误消息

### <a name="mt600x-stripper"></a>MT600x: Stripper

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001：运行版本的 Mono.cecil 不支持程序集剥离

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002：未能去除程序集 `*`。

从应用程序中的程序集去除托管代码（删除 IL 代码）时出错。

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003： [System.unauthorizedaccessexception message]

从应用程序中去除调试符号时出现安全错误。

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx： MSBuild 错误消息

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001：无法解析适用于 WiFi 调试程序设置的主机 Ip。

*MSBuild 任务： DetectDebugNetworkConfigurationTaskBase*

疑难解答步骤：

- 尝试运行 `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` （这应该为你的 IP 地址，而不是明显的错误）。
- 尝试运行 "ping \`主机名\`"，这可能会给出更多的信息，例如： `cannot resolve MyHost.local: Unknown host`

在某些情况下，这是一个 "本地网络" 问题，可以通过在 `/etc/hosts`中添加未知的主机 `127.0.0.1    MyHost.local` 来解决该问题。

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002：此计算机没有任何网络适配器。 在基于 WiFi 的设备上进行调试或事件探查时，这是必需的。

*MSBuild 任务： DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003：应用扩展 "*" 不包含 info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004：应用扩展 "*" 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005：应用扩展 "*" 未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006：应用扩展 "\*" 包含无效的 CFBundleIdentifier （\*），它不会以主应用捆绑的 CFBundleIdentifier （*）开头。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007：应用扩展 "\*" 具有以非法后缀 ". key" 结尾的 CFBundleIdentifier （\*）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008：应用扩展 "*" 未指定 CFBundleShortVersionString。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009：应用扩展 "*" 包含无效的信息。 info.plist：它不包含 NSExtension 字典。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010：应用扩展 "*" 包含无效的信息。 info.plist： NSExtension 字典不包含 NSExtensionPointIdentifier 值。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011： WatchKit 扩展 "*" 的信息无效。 info.plist： NSExtension 字典不包含 NSExtensionAttributes 字典。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012： WatchKit 扩展 "*" 不只包含一个 watch 应用。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013： WatchKit 扩展 "*" 的信息无效。 info.plist： UIRequiredDeviceCapabilities 必须包含 "手表" 功能。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014： Watch 应用 "*" 不包含 info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015： Watch 应用 "*" 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016： Watch 应用 "\*" 包含无效的 CFBundleIdentifier （\*），它不会以主应用捆绑的 CFBundleIdentifier （*）开头。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017： Watch 应用 "\*" 不具有有效的 UIDeviceFamily 值。 应为 "Watch （4）"，但找到的是 "\* （*）"。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018： Watch 应用 "*" 未指定 CFBundleExecutable

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019： Watch 应用 "\*" 包含无效的 WKCompanionAppBundleIdentifier 值（"\*"），它与主应用捆绑包的 CFBundleIdentifier （"*"）不匹配。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020： Watch 应用 "*" 包含无效的信息。 info.plist： WKWatchKitApp 项必须存在并且值为 "true"。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021： Watch 应用 "*" 包含无效的信息。 info.plist： LSRequiresIPhoneOS 项不得存在。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022： Watch 应用 "*" 不包含监视扩展。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023：监视扩展 "*" 不包含 info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024：监视扩展 "*" 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025：监视扩展 "*" 未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026：监视扩展 "\*" 包含无效的 CFBundleIdentifier （\*），它不会以主应用捆绑的 CFBundleIdentifier （*）开头。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027：监视扩展 "\*" 具有以非法后缀 ". key" 结尾的 CFBundleIdentifier （\*）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028：监视扩展 "*" 包含无效的信息。 info.plist：它不包含 NSExtension 字典。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029：监视扩展 "*" 包含无效的信息。 info.plist： NSExtensionPointIdentifier 必须是 ""。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030：监视扩展 "*" 包含无效的信息。 info.plist： NSExtension 字典必须包含 NSExtensionAttributes。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031：监视扩展 "*" 包含无效的信息。 info.plist： NSExtensionAttributes 字典必须包含 WKAppBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032： WatchKit 扩展 "*" 的信息无效。 info.plist： UIRequiredDeviceCapabilities 不应包含 "手表" 功能。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033： Watch 应用 "*" 不包含 info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034： Watch 应用 "*" 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035： Watch 应用 "\*" 不具有有效的 UIDeviceFamily 值。 应为 "\*"，但找到的是 "\* （\*）"。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036： Watch 应用 "*" 未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037： WatchKit 扩展 "{extensionName}" 具有无效的 WKAppBundleIdentifier 值（"\*"），不匹配 Watch 应用的 CFBundleIdentifier （"\*"）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038： Watch 应用 "*" 包含无效的信息。 info.plist： WKCompanionAppBundleIdentifier 必须存在并且必须与主应用捆绑包的 CFBundleIdentifier 匹配。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039： Watch 应用 "*" 包含无效的信息。 info.plist： LSRequiresIPhoneOS 项不得存在。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040：应用程序包 {AppBundlePath} 不包含 info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041： info.plist 路径未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042： info.plist 路径未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043： info.plist 路径未指定 CFBundleSupportedPlatforms。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044： info.plist 路径未指定 UIDeviceFamily。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045：无法识别的格式： *。

*MSBuild 任务： PropertyListEditorTaskBase*

其中 * 可以是：

- 字符串
- array
- dict
- bool
- real
- integer
- date
- data

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046： Add： Entry，*，未正确指定。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047： Add： Entry，* 包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048：添加： * 条目已存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049： Add：不能向父级添加 Entry、*。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050： Delete：不能从父级删除条目。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051： Delete： Entry，* 包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052： Delete： Entry，* 不存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053： Import： Entry，*，未正确指定。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054： Import： Entry，* 包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055：导入：读取文件时出错： *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056： Import：无法将条目（*）添加到父级。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057： Merge：无法将数组项添加到 dict 中。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058： Merge：指定的项必须是一个容器。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059： Merge： Entry，* 包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060： Merge： Entry，* 不存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061： Merge：读取文件时出错： *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062： Set： Entry，*，未正确指定。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063： Set： Entry，* 包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064： Set： Entry，* 不存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065：未知 PropertyList 编辑器操作： *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066：加载 "*" 时出错： *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067：保存 "*" 时出错： *。

*MSBuild 任务： PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx：运行时错误消息

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001：本机 Xamarin 运行时和 monotouch.dialog 之间的版本不匹配。 请重新安装 Xamarin。

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002：在类型 "\*" 中找不到方法 "\*"。

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003：无法在类型 "\*" 上找到封闭式泛型方法 "\*"。

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004：无法为本机对象 0x * （类型为 "\*"）创建 \* 的实例，因为此本机对象已存在另一个实例（类型为 \*）。

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005：包装类型 "\*" 缺少其本机 ObjectiveC 类 "\*"。

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006：无法在类型 "\*" 上找到选择器 "\*"

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007：无法获取类型 "\*" 上选择器 "\*" 的方法描述符，因为选择器与方法不对应

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008：为 \* 位编译了 Xamarin 的加载版本，而进程 \* 位。 请在 http://bugzilla.xamarin.com提交 bug。

这表明生成过程中出现了错误。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009：找不到用于委托方法的转换方法的块 *。* s 参数 # *。 请在 http://bugzilla.xamarin.com提交 bug。

这表示未正确绑定 API。 如果这是 Xamarin 公开的 API，请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。 如果是第三方绑定，请与供应商联系。

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010： Xamarin 之间的本机类型大小不匹配。[iOS |Mac] .dll 和正在执行的体系结构。 Xamarin.[iOS |Mac] .dll 为 * 位生成，而当前进程为 * 位。

这表明生成过程中出现了错误。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011：找不到*该方法的*返回值的委托来阻止转换特性（[DelegateProxy]）。 请在 http://bugzilla.xamarin.com提交 bug。

Xamarin 无法在运行时定位所需的方法（将委托转换为块）。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012：方法的返回值的 DelegateProxyAttribute 无效 *。* ：委托为 null。 请在 http://bugzilla.xamarin.com提交 bug。

相关方法的 DelegateProxy 属性无效。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013：方法的返回值的 DelegateProxyAttribute 无效 *。* ：委托（{2}）指定一个不包含 "Handler" 字段的类型。 请在 http://bugzilla.xamarin.com提交 bug。

相关方法的 `[DelegateProxy]` 属性无效。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014：方法的返回值的 DelegateProxyAttribute 无效 *。* ：委托的（{2}） "Handler" 字段为 null。 请在 http://bugzilla.xamarin.com提交 bug。

相关方法的 `[DelegateProxy]` 属性无效。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015：方法的返回值的 DelegateProxyAttribute 无效 *。* ：委托的（{2}） "Handler" 字段不是一个委托，它是一个 *。 请在 http://bugzilla.xamarin.com提交 bug。

相关方法的 DelegateProxy 属性无效。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016：无法将此方法的返回值转换为块的委托 *。* 因为输入不是一个委托，所以它是一个 *。 请在 http://bugzilla.xamarin.com提交 bug。

相关方法的 `[DelegateProxy]` 属性无效。

这通常表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018：内部一致性错误。 请在 http://bugzilla.xamarin.com提交 bug 报告。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019：在加载的程序集中找不到程序集 *。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020：在程序集 \*中找不到具有 MetadataToken \* 的模块。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021：未知的隐式标记类型： *。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022：令牌引用应 \* 为 \*，但它是 \*。 请在 http://bugzilla.xamarin.com提交 bug 报告。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023：需要实例对象来构造开放式泛型方法的封闭式泛型方法： \* （令牌引用： \*）。 请在 http://bugzilla.xamarin.com提交 bug 报告。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smart_type-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024：找不到智能枚举 "{smart_type}" 的有效扩展类型。 请在 https://bugzilla.xamarin.com提交 bug。

这表示 Xamarin 中的 bug。 请在[github](https://github.com/xamarin/xamarin-macios/issues/new)上发布新问题。
