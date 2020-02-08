---
title: ProGuard
description: Xamarin.Android ProGuard 是一个 Java 类文件压缩器、优化器和预验证器。 它会检测和删除未使用的代码，分析和优化字节码。 本指南阐释了 ProGuard 的工作原理、如何在项目中启用它，以及如何进行配置。 同时提供了几个 ProGuard 配置示例。
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 28f48766dc62ac31fc357410eac27c33c9cae6d8
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940683"
---
# <a name="proguard"></a>ProGuard

_Xamarin.Android ProGuard 是一个 Java 类文件压缩器、优化器和预验证器。它会检测和删除未使用的代码，分析和优化字节码。本指南阐释了 ProGuard 的工作原理、如何在项目中启用它，以及如何进行配置。同时提供了几个 ProGuard 配置示例。_

## <a name="overview"></a>概述

ProGuard 从打包的应用程序中检测并删除未使用的类、字段、方法和属性。 它甚至可对引用的库执行相同操作（这有助于避免 64k 引用限制）。 Android SDK 中的 ProGuard 工具还将优化字节码和删除未使用的代码说明。 ProGuard 读取输入文件，然后将其压缩、优化和预验证，并将结果写入一个或多个输出文件。 

ProGuard 使用以下步骤处理输入 APK： 

1. **压缩步骤** &ndash; ProGuard 以递归方式确定使用的类和类成员。 将丢弃所有其他类和类成员。 

2. **优化步骤** &ndash; ProGuard 进一步优化代码。 
    在其他优化中，可将非入口点的类和方法设置为私有、静态或最终，可删除未使用的参数，并且可内联一些方法。 

3. **Obfuscation step** &ndash; 在本机 Android 开发中，ProGuard 重命名非入口点的类和类成员。 保留入口点，确保它们仍可通过其原始名称访问。 但是，Xamarin.Android 并不支持此步骤，因为该应用是使用中间语言 (IL) 编译的。

4. **预验证步骤** &ndash; 在运行时前检查 Java 字节码，并对 Java VM 权益的类文件进行批注。 只有此步骤无需知道入口点。 

上述每个步骤均*可选*。 Xamarin.Android ProGuard 要使用其中的部分步骤，详见下一节。 

## <a name="proguard-in-xamarinandroid"></a>Xamarin.Android 中的 ProGuard

Xamarin.Android ProGuard 配置不会模糊处理 APK。 事实上，无法通过 ProGuard 进行模糊处理（即使使用自定义配置文件）。 因此，Xamarin.Android 的 ProGuard 只执行**压缩**和**优化**步骤： 

[![收缩和优化步骤](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

使用 ProGuard 前，必须知道其在 `Xamarin.Android` 生成过程中的工作原理。 此过程使用两个独立的步骤： 

1. Xamarin Android 链接器

2. ProGuard

接下来将说明上述各步骤。

### <a name="linker-step"></a>链接器步骤

Xamarin.Android 链接器使用应用程序的静态分析来确定以下内容： 

- 实际使用的程序集。

- 实际使用的类型。

- 实际使用的成员。 

将始终在 ProGuard 步骤前运行链接器。 因此，链接器可剥因此，链接器可剥离想要 ProGuard 在其上运行的程序集/类型/成员。 （若要详细了解 Xamarin.Android 中的链接，请参阅[在 Android 上链接](~/android/deploy-test/linker.md)。）

### <a name="proguard-step"></a>ProGuard 步骤

链接器步骤成功完成后，运行 ProGuard 删除未使用的 Java 字节码。 此步骤用于优化 APK。 

## <a name="using-proguard"></a>使用 ProGuard

必须先启用 ProGuard，才可在应用项目中使用它。 接下来，可让 Xamarin.Android 生成过程使用默认的 ProGuard 配置文件，也可自行创建自定义配置文件供 ProGuard 使用。 

### <a name="enabling-proguard"></a>启用 ProGuard

在应用项目中使用以下步骤启用 ProGuard：

1. 确保项目设置为“发布”配置（这很重要，因为必须先运行链接器才能运行 ProGuard）： 

    [![选择发布配置](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2. 从“属性”>“Android 选项”窗口上的“代码 shrinker”下拉列表中选择“ProGuard”： 

    [![已选择 Proguard 代码 shrinker](proguard-images/03-enable-proguard-shrinker-sml.png)](proguard-images/03-enable-proguard-shrinker.png#lightbox)

对于大多数 Xamarin.Android 应用，Xamarin.Android 提供的默认 ProGuard 配置文件足以删除所有（仅）未使用的代码。 若要查看默认 ProGuard 配置，请打开 **obj\\Release\\proguard\\proguard_xamarin.cfg** 处的文件。

以下示例说明了典型生成的 proguard_xamarin.cfg 文件：

```cfg
# This is Xamarin-specific (and enhanced) configuration.

-dontobfuscate

-keep class mono.MonoRuntimeProvider { *; <init>(...); }
-keep class mono.MonoPackageManager { *; <init>(...); }
-keep class mono.MonoPackageManager_Resources { *; <init>(...); }
-keep class mono.android.** { *; <init>(...); }
-keep class mono.java.** { *; <init>(...); }
-keep class mono.javax.** { *; <init>(...); }
-keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk.GameViewBase { *; <init>(...); }
-keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk_1_0.GameViewBase { *; <init>(...); }

-keep class android.runtime.** { <init>(***); }
-keep class assembly_mono_android.android.runtime.** { <init>(***); }
# hash for android.runtime and assembly_mono_android.android.runtime.
-keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
-keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

# Android's template misses fluent setters...
-keepclassmembers class * extends android.view.View {
   *** set*(***);
}

# also misses those inflated custom layout stuff from xml...
-keepclassmembers class * extends android.view.View {
   <init>(android.content.Context,android.util.AttributeSet);
   <init>(android.content.Context,android.util.AttributeSet,int);
}
```

下一节将介绍如何创建自定义 ProGuard 配置文件。 

### <a name="customizing-proguard"></a>自定义 ProGuard

或者，可添加自定义 ProGuard 配置文件，实现对 ProGuard 工具的更多掌控。 例如，你可能想就要保留的类显式通知 ProGuard。 为此，请新建 **.cfg** 文件，并在**解决方案资源管理器**的“属性”窗格中应用 `ProGuardConfiguration` 生成操作： 

[![已选中“ProguardConfiguration 生成操作”](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

请记住，该配置文件不会替换 Xamarin.Android proguard_xamarin.cfg 文件，因为 ProGuard 将使用这两者。 

有些情况下，ProGuard 可能无法正确分析应用程序，它可能会删除应用程序实际需要的代码。 若发生此情况，可将 `-keep` 行添加到自定义 ProGuard 配置文件： 

```
-keep public class MyClass
```

本例中，`MyClass` 设置为想让 ProGuard 跳过的类的实际名称。

还可使用 `[Register]` 注释来注册自己的名称，并使用这些名称来自定义 ProGuard 规则。 可为 Adapter、View、BroadcastReceiver、Service、ContentProvider、Activity 和 Fragment 注册名称。 有关使用 `[Register]` 自定义属性的详细信息，请参阅[使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

### <a name="proguard-options"></a>ProGuard 选项

ProGuard 提供了许多选项，可配置实现更精细的操作控制。 [ProGuard 手册](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html)提供了 ProGuard 用法的完整参考文档。 

Xamarin.Android 支持以下 ProGuard 选项： 

- [输入/输出选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

- [保留选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

- [压缩选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

- [常规选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

- [类路径](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

- [文件名](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

- [文件筛选器](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

- [筛选器](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

- [`Keep` 选项概述](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

- [保留选项修饰符](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

- [类规范](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

Xamarin.Android *忽略*以下选项：

- [优化选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

- [模糊处理选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

- [预验证选项](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)

## <a name="proguard-and-android-nougat"></a>ProGuard 和 Android Nougat

如果尝试在 Android 7.0 或更高版本上使用 ProGuard，必须下载较新版本的 ProGuard，因为 Android SDK 未提供与 JDK 1.8 兼容的新版本。

可使用此 [NuGet 包](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0)安装 `proguard.jar` 的较新版本。 有关更新默认 Android SDK `proguard.jar` 的详细信息，请参阅此[堆栈溢出](https://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706)讨论。

可在 [SourceForge 页面](https://sourceforge.net/projects/proguard/files/)找到所有版本的 ProGuard。 

## <a name="example-proguard-configurations"></a>ProGuard 配置示例

下面列出了两个 ProGuard 配置文件示例。 请注意，在这些情况下，Xamarin.Android 生成过程将提供**输入**、**输出**和**库** jar。 因此，可专注于其他选项，如 `-keep`。 

### <a name="a-simple-android-activity"></a>一个简单的 Android 活动

下例演示了一个简单 Android 活动的配置：

```
-injars  bin/classes
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

-keep public class mypackage.MyActivity
```

### <a name="a-complete-android-application"></a>一个完整的 Android 应用程序

下例演示了一个完整 Android 应用的配置：

```
-injars  bin/classes
-injars  libs
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic
-keepattributes *Annotation*

-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider

-keep public class * extends android.view.View {
public <init>(android.content.Context);
public <init>(android.content.Context, android.util.AttributeSet);
public <init>(android.content.Context, android.util.AttributeSet, int);
public void set*(...);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * implements android.os.Parcelable {
static android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
public static <fields>;
}
```

## <a name="proguard-and-the-xamarinandroid-build-process"></a>ProGuard 和 Xamarin.Android 生成过程

以下部分介绍了 ProGuard 在Xamarin.Android **发布**生成期间的运行方式。

### <a name="what-command-is-proguard-running"></a>ProGuard 正在运行什么命令？

ProGuard 只是随附 Android SDK 提供的 `.jar`。 因此，它会在命令中被调用： 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>ProGuard 任务

ProGuard 任务位于 **Xamarin.Android.Build.Tasks.dll** 程序集中。 它是 `_CompileToDalvikWithDx` 目标的一部分，后者则是 `_CompileDex` 目标的一部分。 

以下列表提供了使用“文件”>“新建项目”创建新项目后生成的默认参数示例： 

```
ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
AndroidSdkDirectory = C:\Android\android-sdk\
JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
ClassesOutputDirectory = obj\Release\android\bin\classes
AcwMapFile = obj\Release\acw-map.txt
ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
ProGuardConfigurationFiles

    {sdk.dir}tools\proguard\proguard-android.txt;
    {intermediate.common.xamarin};
    {intermediate.references};
    {intermediate.application};
    ;

JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
DumpOutput = obj\Release\proguard\dump.txt
PrintSeedsOutput = obj\Release\proguard\seeds.txt
PrintUsageOutput = obj\Release\proguard\usage.txt
PrintMappingOutput = obj\Release\proguard\mapping.txt
```

下一示例展示了从 IDE 运行的典型 ProGuard 命令：

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>疑难解答

### <a name="file-issues"></a>文件问题

当 ProGuard 读取其配置文件时，可能会显示以下错误消息： 

```
Unknown option '-keep' in line 1 of file 'proguard.cfg'
```

此问题通常发生在 Windows 上，因为 `.cfg` 文件编码错误。 ProGuard 不能处理字节顺序标记 (BOM)，它可能出现在文本文件中。 如果存在 BOM，ProGuard 将退出并显示上述错误。 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

为避免此问题，请从允许不使用 BOM 保存文件的文本编辑器中编辑自定义配置文件。 若要解决此问题，请确保文本编辑器的编码设置为 `UTF-8`。 例如，保存文件时，文本编辑器 [Notepad++](https://notepad-plus-plus.org/) 可通过选择“编码”&gt;“无 BOM 时采用 UTF-8 编码”在没有 BOM 的情况下保存文件。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

为避免此问题，请从可忽略 BOM 的文本编辑器保存自定义配置文件。 

-----

### <a name="other-issues"></a>其他问题

ProGuard [疑难解答](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html)页面讨论了使用 ProGuard 时可能遇到的常见问题（及解决方案）。

## <a name="summary"></a>总结

本指南阐释了 ProGuard 在 Xamarin.Android 中的工作原理、如何在应用项目中启用它，以及如何进行配置。 提供了示例 ProGuard 配置，并描述了常见问题的解决方案。 有关 ProGuard 工具和 Android 的详细信息，请参阅[压缩代码和资源](https://developer.android.com/tools/help/proguard.html)。 

## <a name="related-links"></a>相关链接

- [做好应用程序发布准备](~/android/deploy-test/release-prep/index.md)
