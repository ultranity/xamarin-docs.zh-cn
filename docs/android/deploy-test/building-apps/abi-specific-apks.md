---
title: 构建特定于 ABI 的 APK
description: 本文讨论如何构建一个使用 Xamarin.Android 以单个 ABI 为目标的 APK。
ms.prod: xamarin
ms.assetid: D21B195B-4530-4EB2-8704-5C4349A2CDD8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 0520439b89458b7f73a025cd8d6b2cf8fc41dac0
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940639"
---
# <a name="building-abi-specific-apks"></a>构建特定于 ABI 的 APK

_本文讨论如何构建一个使用 Xamarin.Android 以单个 ABI 为目标的 APK。_

## <a name="overview"></a>概述

在某些情况下，应用程序拥有多个 APK 可能更有利 - 每个 APK 都使用相同的密钥存储进行签名，并共享相同的软件包名称，但它是针对特定设备或 Android 配置进行编译的。 这不是推荐的方法 - 拥有一个可以支持多种设备和配置的 APK 是非常简单的。 在某些情况下，创建多个 APK 可能很有用，例如：

- 减小 APK 的大小 - Google Play 对 APK 文件强加了 100MB 大小的限制。 创建特定于设备的 APK 可以减小 APK 的大小，因为你只需为应用程序提供一部分资产和资源。

- 支持不同的 CPU 体系结构 - 如果你的应用程序具有特定 CPU 的共享库，则只能分发该 CPU 的共享库。

多个 APK 可能会使分发变得复杂 - 这是 Google Play 解决的问题。 Google Play 将确保根据应用程序的版本代码和 AndroidManifest.XML 中包含的其他元数据将正确的 APK 传递到设备。 有关 Google Play 如何支持应用程序多个 APK 的具体详细信息和限制，请查阅[关于多个 APK 支持的 Google 文档](https://developer.android.com/google/play/publishing/multiple-apks.html)。

本指南介绍如何为 Xamarin.Android 应用程序构建多个 APK 的脚本，每个 APK 都针对一个特定的 ABI。 它包含以下主题：

1. 为 APK 创建一个唯一的版本代码。
1. 创建一个将用于此 APK 的 AndroidManifest.XML 的临时版本。
1. 使用上一步中的 AndroidManifest.XML 构建应用程序。
1. 通过对 APK 进行签名并使用 zipalign 为其优化来准备发布。

本指南最后将演示如何使用 [Rake](https://martinfowler.com/articles/rake.html) 编写这些步骤的脚本。

### <a name="creating-the-version-code-for-the-apk"></a>为 APK 创建版本代码

Google 为使用七位数版本代码的版本代码推荐了一种特定算法（请参阅[多 APK 支持文档](https://developer.android.com/google/play/publishing/multiple-apks.html)中的“使用版本代码方案”一节）。
通过将此版本代码方案扩展为八位数字，可以将一些 ABI 信息包含在版本代码中，以确保 Google Play 将正确的 APK 分发到设备。 以下列表解释了这个八位数版本代码格式（从左到右编制索引）：

- **索引 0**（下图中的红色）&ndash; ABI 的整数：
  - 1 &ndash; `armeabi`
  - 2 &ndash; `armeabi-v7a`
  - 6 &ndash; `x86`

- **索引 1-2**（下图中的橙色）&ndash; 应用程序支持的最低 API 级别。

- **索引 3-4**（下图中的蓝色）&ndash; 支持的屏幕大小：
  - 1 &ndash; 小
  - 2 &ndash; 常规
  - 3 &ndash; 大
  - 4 &ndash; 加大

- **索引 5-7**（下图中的绿色）&ndash; 版本代码的唯一编号。 
    这由开发人员设置。 它应为应用程序的每个公开发布版本增加。

下图说明了上面列表中描述的每个代码的位置：

[![八位数版本代码格式的图示，用颜色编码](abi-specific-apks-images/image00.png)](abi-specific-apks-images/image00.png#lightbox)

Google Play 确保根据 `versionCode` 和 APK 配置将正确的 APK 发送到设备。 将具有最高版本代码的 APK 发送到设备。 例如，应用程序可能有三个 APK，其版本代码如下：

- 11413456 - ABI 是 `armeabi`；针对 API 级别 14；小屏幕到大屏幕；版本号为 456。
- 21423456 - ABI 是 `armeabi-v7a`；针对 API 级别 14；常规 &amp; 大屏幕；版本号为 456。
- 61423456 - ABI 是 `x86`；针对 API 级别 14；常规 &amp; 大屏幕；版本号为 456。

要继续使用此示例，假设已修复一个特定于 `armeabi-v7a` 的错误。 应用版本增加到 457，并且将 `android:versionCode` 设置为 21423457 来构建新的 APK。 `armeabi` 和 `x86` 版本的版本代码将保持不变。

现在假设 x86 版本接收一些针对更新 API（API 级别 19）的更新或错误修复，使该应用版本成为 500。 当 armeabi/armeabi-v7a 保持不变时，新的 `versionCode` 将更改为 61923500。 此时，版本代码将是：

- 11413456 - ABI 是 `armeabi`；针对 API 级别 14；小屏幕到大屏幕；版本名称为 456。
- 21423457 - ABI 是 `armeabi-v7a`；针对 API 级别 14；常规 &amp; 大屏幕；版本名称为 457。
- 61923500 - ABI 是 `x86`；针对 API 级别 19；常规 &amp; 大屏幕；版本名称为 500。

手动维护这些版本代码可能会对开发人员带来沉重的负担。 计算正确的 `android:versionCode` 然后构建 APK 的过程应该是自动执行的。
本文末尾的演练将举例介绍如何执行此操作。

### <a name="create-a-temporary-androidmanifestxml"></a>创建临时的 AndroidManifest.XML

虽然不是绝对必要，但为每个 ABI 创建临时的 **AndroidManifest.XML** 可以帮助防止由于在 APK 之间泄露信息可能出现的问题。 例如，`android:versionCode` 属性对于每个 APK 都是唯一的，这一点至关重要。

完成此任务的方式取决于所涉及的脚本系统，但通常涉及获取开发过程中使用的 Android 清单副本，修改副本，然后在构建过程中使用修改后的清单。

### <a name="compiling-the-apk"></a>编译 APK

如以下示例命令行中所示，通过使用 `xbuild` 或 `msbuild` 能最好地完成为每个 ABI 构建 APK 的任务：

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:Package /p:AndroidSupportedAbis=<TARGET_ABI> /p:IntermediateOutputPath=obj.<TARGET_ABI>/ /p:AndroidManifest=<PATH_TO_ANDROIDMANIFEST.XML> /p:OutputPath=bin.<TARGET_ABI> /p:Configuration=Release <CSPROJ FILE>
```

下表解释了每个命令行参数：

- `/t:Package`&ndash; 创建使用调试密钥存储进行签名的 Android APK

- `/p:AndroidSupportedAbis=<TARGET_ABI>`&ndash; 这是要面向的 ABI。 必须为 `armeabi`、`armeabi-v7a` 或 `x86` 之一。

- `/p:IntermediateOutputPath=obj.<TARGET_ABI>/`&ndash; 这是保存作为版本一部分创建的中间文件的目录。 如果需要，Xamarin.Android 将创建一个以 ABI 命名的目录，例如 `obj.armeabi-v7a`。 建议为每个 ABI 使用一个文件夹，因为这样可以防止在版本之间“泄露”文件导致的问题。 请注意，此值以目录分隔符（在 OS X 的情况下为 `/`）结束。

- `/p:AndroidManifest`&ndash; 此属性指定将在构建期间使用的 AndroidManifest.XML 文件的路径。

- `/p:OutputPath=bin.<TARGET_ABI>`&ndash; 这是容纳最终 APK 的目录。 Xamarin.Android 将创建一个以 ABI 命名的目录，例如 `bin.armeabi-v7a`。

- `/p:Configuration=Release`&ndash; 执行 APK 的发布版本。 调试版本可能无法上传到 Google Play。

- `<CS_PROJ FILE>`&ndash; 这是 Xamarin.Android 项目的 `.csproj` 文件的路径。

### <a name="sign-and-zipalign-the-apk"></a>为 APK 签名并使用 Zipalign 为其优化

在通过 Google Play 发布 APK 之前，必须先为其签名。 这可以通过使用属于 Java 开发人员工具包的 `jarsigner` 应用程序来执行。 下面的命令行演示如何在命令行中使用 `jarsigner`：

```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <PATH/TO/KEYSTORE> -storepass <PASSWORD> -signedjar <PATH/FOR/SIGNED_JAR> <PATH/FOR/JAR/TO/SIGN> <NAME_OF_KEY_IN_KEYSTORE>
```

必须先使用 Zipalign 为所有的 Xamarin.Android 应用程序优化，然后才能在设备上运行。 这是要使用的命令行格式：

```shell
zipalign -f -v 4 <SIGNED_APK_TO_ZIPALIGN> <PATH/TO/ZIP_ALIGNED.APK>
```

## <a name="automating-apk-creation-with-rake"></a>通过 Rake 自动创建 APK

示例项目 [OneABIPerAPK](https://github.com/xamarin/monodroid-samples/tree/master/OneABIPerAPK) 是一个简单的 Android 项目，演示如何计算 ABI 特定的版本号并为以下所有的 ABI 构建三个独立的 APK：

- armeabi
- armeabi-v7a
- x86

示例项目中的 [rakefile](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb) 执行前几节中描述的所有步骤：

1. 为 APK [创建 android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L30)。

1. 将 [android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L55) 写入该 APK 的自定义 **AndroidManifest.XML**。

1. 为 Xamarin.Android 项目[编译发布版本](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L63)，该项目特别面向 ABI 并使用在上一步中创建的 **AndroidManifest.XML**。

1. 使用生产密钥存储[为 APK 签名](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L66)。

1. 使用 [Zipalign](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L67) 优化 APK。

要构建应用程序的所有 APK，请从命令行运行 `build` Rake 任务：

```shell
$ rake build
==> Building an APK for ABI armeabi with ./Properties/AndroidManifest.xml.armeabi, android:versionCode = 10814120.
==> Building an APK for ABI x86 with ./Properties/AndroidManifest.xml.x86, android:versionCode = 60814120.
==> Building an APK for ABI armeabi-v7a with ./Properties/AndroidManifest.xml.armeabi-v7a, android:versionCode = 20814120.
```

rake 任务完成后，将有三个包含文件 `xamarin.helloworld.apk` 的 `bin` 文件夹。 下一个屏幕截图显示了每个文件夹及其内容：

[![包含 xamarin.helloworld.apk 的平台特定文件夹的位置](abi-specific-apks-images/image01.png)](abi-specific-apks-images/image01.png#lightbox)

> [!NOTE]
> 本指南中概述的构建过程可以在许多不同的构建系统之一中实现。 尽管我们没有预先编写的示例，但 [Powershell](https://technet.microsoft.com/scriptcenter/powershell.aspx) / [psake](https://github.com/psake/psake) 或 [Fake](https://fsharp.github.io/FAKE/) 应该也适用。

## <a name="summary"></a>总结

本指南提供了一些关于如何创建针对指定 ABI 的 Android APK 的建议。 此外，还讨论了创建 `android:versionCodes` 的一种可能的方案，该方案将识别该 APK 所针对的 CPU 体系结构。 演练包括一个使用 Rake 编写脚本的示例项目。

## <a name="related-links"></a>相关链接

- [OneABIPerAPK（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/oneabiperapk)
- [发布应用程序](~/android/deploy-test/publishing/index.md)
- [面向 Google Play 的多 APK 支持](https://developer.android.com/google/play/publishing/multiple-apks.html)
