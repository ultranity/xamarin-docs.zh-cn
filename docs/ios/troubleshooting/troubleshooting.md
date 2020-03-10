---
title: Xamarin 的故障排除提示
description: 本文档提供了在开发 Xamarin iOS 应用程序的过程中用于故障排除的各种提示。 它描述了特定错误消息以及其他潜在问题。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 716999002cf90b50b90f4924adc11555cc43717f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915372"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Xamarin 的故障排除提示

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin 无法解析 ValueTuple

发生此错误的原因是与 Visual Studio 不兼容。

- **Visual Studio 2017 Update 1** （版本15.1 或更低版本）仅与**ValueTuple NuGet 4.3.0** （或更早版本）兼容。

- **Visual Studio 2017 Update 2** （版本15.2 或更高版本）仅与**ValueTuple NuGet 4.3.1**或更高版本兼容。

请选择与你的 Visual Studio 2017 安装相对应的正确 ValueTuple NuGet。

## <a name="receiving-error-retrieving-update-information-error-message"></a>正在接收 "检索更新信息时出错" 错误消息

尝试更新软件时，如果显示此错误消息，请尝试重新启动 IDE 并注销，然后重新登录到 IDE 中的帐户。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何实现通过 Interface Builder 创建插座或操作？

随着 Visual Studio for Mac 和 Visual Studio 中的 Xamarin Designer for iOS 的引入，Xamarin iOS 开发人员现在可以利用通过情节提要和 xib 创建 UI。 有关使用设计器的详细信息，请参阅[iOS](~/ios/get-started/hello-ios/index.md)指南。

有关在 IB 中使用输出口和操作的详细信息，请参阅 Apple 的[插座](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)和[操作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html)指南。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>Encoding.getencoding 抛出 NotSupportedException

你可能正在使用默认情况下未添加的编码。 查看[国际化](~/ios/app-fundamentals/localization/index.md)页面，了解如何添加对更多编码的支持。

## <a name="systemmissingmethodexception-anything-else"></a>MissingMethodException （其他任何内容）

此成员可能已被链接器删除，因此在运行时不存在于程序集中。  有多种解决方案：

- 向成员添加[`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute)特性。  这会阻止链接器删除它。
- 调用[**mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29)时，请使用 **-nolink**或 **-linksdkonly**选项：
  - **-nolink**禁用所有链接。
  - **-linksdkonly**将仅链接 xamarin 提供的程序集（如**xamarin**），同时保留用户创建的程序集中的所有类型（即应用项目）。

请注意，对程序集进行了链接，使生成的可执行文件更小;因此，禁用链接可能导致比预期更大的可执行文件。

## <a name="you-are-getting-a-modelnotimplementedexception"></a>正在获取 ModelNotImplementedException

如果您收到此异常，则表示您正在调用 base。重写模型的类上的方法（）。 不需要在模型的类中调用基方法（这些类是用 [Model] 特性标记的类）。

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>此类不符合键 XXXX 的键值编码

如果在加载笔尖文件时收到此错误，则表示在托管类上找不到值 XXXX。 这意味着缺少如下声明：

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

对于添加到 `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` 文件中 Visual Studio for Mac 的任何 XIB 文件，将自动 Visual Studio for Mac 生成上述定义。

此外，包含上述代码的类型必须是[NSObject](xref:Foundation.NSObject)的子类。  如果包含类型在命名空间中，则它还应具有一个[[Register]](xref:Foundation.RegisterAttribute)特性，该特性提供不带命名空间的类型名称（因为 Interface Builder 不支持类型中的命名空间）：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Interface Builder 文件中的未知类 XXXX

如果在接口生成器文件中定义类，但没有在C#代码中提供它的实际实现，则会生成此错误。

需要添加一些类似于下面的代码：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>MissingMethodException：找不到 Foo：： ctor （system.string）的构造函数

当代码尝试实例化从 Interface Builder 文件中引用的类的实例时，将在运行时生成此错误。 这意味着你忘记添加使用单个 IntPtr 作为参数的构造函数。

带有 IntPtr 句柄的构造函数用于绑定托管对象及其非托管表示形式。

若要解决此问题，请将以下代码行添加到类 Foo. Bar：

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>类型 {Foo} 不包含 `GetNativeField` 的定义，并且找不到类型为 {Foo} 的扩展方法 `GetNativeField`

如果在设计器生成的文件（*. xib.designer.cs）中出现此错误，则表示以下两种情况之一：

 **1）缺少分部类或基类**

设计器生成的分部类在用户代码中必须具有相应的分部类，这些类从 `NSObject`的某些子类继承，通常 `UIViewController`。 确保为该类型提供了该错误的类。

 **2）更改了默认命名空间**

设计器文件是使用项目的默认命名空间设置生成的。 如果更改了这些设置，或重命名了项目，则生成的分部类可能不再与它们的用户代码对应的命名空间相同。

命名空间设置可在 "项目选项" 对话框中找到。 默认命名空间位于 "**常规-> 主设置**" 部分中。 如果为空，则将项目名称用作默认值。 可以在**源代码-> .Net 命名策略**"部分中找到更高级的命名空间设置。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>操作警告：从未使用私有方法 "Foo"。 (CS0169)

接口生成器文件的操作在运行时通过反射连接到小组件，因此应该出现此警告。

如果要针对这些方法仅禁止显示此警告，可以在操作中使用 "#pragma 警告禁用 0169" #pragma 警告启用 0169 "; 如果想要为整个项目禁用此警告，请将0169添加到" 编译器选项 "中的" 忽略警告 "字段（不推荐）。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失败，出现以下消息：无法打开程序集 "/path/to/yourproject.exe"

如果看到此错误消息，则通常是您的项目的绝对路径包含空格。 此问题将在未来的 Xamarin 版本中得到解决，但你可以通过将项目移到没有空格的文件夹来解决此问题。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>你的 sqlite3 版本已过时-请升级到至少 v 3.5.0！

执行以下所有操作时，会发生这种情况：

1. 使用 Mono
1. 使用 Mac OS X Leopard （10.5）
1. 在模拟器中运行应用。

问题在于单声道是指 OS X `libsqlite3.dylib`，而不是 iPhoneSimulator 的 `libsqlite3.dylib` 文件。 您的应用程序*将*在设备上运行，而不是模拟器。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>部署到设备失败，出现系统错误。异常： AMDeviceInstallApplication 返回3892346901

此错误表示证书/捆绑 id 的代码签名配置与设备上安装的预配配置文件不匹配。  确认在项目选项中选择了适当的证书-> iPhone 捆绑签名，以及在项目选项-> iPhone 应用程序中指定的正确的捆绑 id

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>代码完成在 Visual Studio for Mac 中不起作用

确保使用最新版本的 Visual Studio for Mac 和 Xamarin

如果问题仍然存在，请提交[bug](https://monodevelop.com/Developers#Reporting_Bugs)，附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{timestamp} .log**，并附加**组件-{TIMESTAMP} .log**日志文件。

如果所有其他操作都失败，可以尝试删除代码完成缓存，以便重新生成它：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

请小心键入此命令，否则可能会意外地删除重要文件。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>复制文本时出现 Visual Studio for Mac 崩溃

常用的 Mac 实用程序 QuickSilver、Google 工具栏和启动栏具有 Visual Studio for Mac 的内存损坏的剪贴板功能。 在这些选项中，你可以将 Visual Studio for Mac 列出为不应干扰的进程。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio for Mac 投诉原因有关 Mono 2.4 的要求

如果由于最近的更新而更新了 Visual Studio for Mac，并且当你尝试再次启动时，投诉原因不存在 Mono 2.4，你只需[升级 mono 2.4 安装](http://www.go-mono.com/mono-downloads/download.html)即可。  

Mono 2.4.2.3 _6 修复了一些 Visual Studio for Mac 无法可靠运行的重要问题，有时会在启动时挂起 Visual Studio for Mac 或阻止生成代码完成数据库。

安装新 Mono 后，Visual Studio for Mac 将按预期方式启动。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>中的断言。/../../../mono/metadata/generic-sharing.c：704，不满足条件 "oti"

如果你正在接收以下 stack 跟踪：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

这意味着你要将使用 thumb 代码编译的静态库链接到你的项目中。 从 iPhone SDK 版本3.1 （在撰写本文时，或更高版本），Apple 在使用 Thumb 代码（静态库）链接非 Thumb 代码（Xamarin）时在其链接器中引入了 bug。你将需要与静态库的非 Thumb 版本链接，以减轻此问题。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>ExecutionEngineException：尝试 JIT 编译方法（由托管到托管的包装） Foo []： System.web. get_Count （）

[] 后缀指示你或类库正在通过泛型集合（如 IEnumerable < >、ICollection < > 或 IList < > 调用数组中的方法。 作为一种解决方法，你可以通过自行调用方法，并确保在触发异常的调用之前执行此代码，从而显式强制 AOT 编译器包含此类方法。 在这种情况下，可以编写：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

这将强制 AOT 编译器包含 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 源编辑器非常慢

有时 Visual Studio for Mac 的源编辑器会变得非常慢，因此在键入字符之间等待几秒钟。

此问题很少发生，并且很难重现-在 Visual Studio for Mac 重新启动之后，通常无法在同一台计算机上重现此问题。 出于此原因，如果你可以在重新启动 Visual Studio for Mac 之前执行几个调试步骤，则会非常感激，并将结果发送给我们。

1. 请尝试关闭编辑器选项卡，然后重新打开它。 是否需要花一些时间来编辑或移动插入符号，直到减速再次出现？
1. 使用 "Quartz 调试" 开发人员工具（你可以使用聚光灯查找）禁用 "横梁同步"，并检查源编辑器性能是否还原为正常。
1. 试用始终禁用了横梁同步的重复步骤（1）。
1. 如果编辑器挂起了几秒钟以上，请尝试在终端中运行 "killall-QUIT [Visual Studio for Mac]"，同时将其挂起。 当编辑器挂起时，可能很难终止 kill 命令，但执行此操作非常重要，因为该命令强制 Mono 将所有线程的堆栈跟踪写入 MD 日志，这样，我们可以使用它们来发现当 XS 挂起时线程处于哪些状态。

请附加 XS 日志， **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **ANDROIDTOOLS-{timestamp} .Log**，AND**组件-{timestamp} .log** （在旧版本的 XS/MonoDevelop 中，只需 send **~/Library/Logs/MonoDevelop-（3.0 | 2.8 | 2.6）/MonoDevelop.log**）。

> [!NOTE]
> 上述问题已在 XS 2.2 最终版本中修复 * *

## <a name="compiled-application-is-very-large"></a>已编译的应用程序非常大

为了支持调试，调试版本包含其他代码。 在发布模式下生成的项目是大小的一小部分。

自 Xamarin. iOS 1.3，调试版本包括针对 Mono 的每个组件（每类框架的每个方法）的调试支持。  

使用 Xamarin 1.4，我们将介绍一种更细化的调试方法，默认情况下，它只是为您的代码和库提供调试检测，而不是为所有[Mono 程序集](~/cross-platform/internals/available-assemblies.md)提供调试工具（这仍是可行的，但您必须选择对这些程序集进行调试）。

## <a name="installation-hangs"></a>安装挂起

如果你正在运行 iPhone 模拟器，则 Mono 和 Xamarin iOS 安装程序都挂起。 此问题并不局限于 Mono 或 Xamarin，这是在安装时在 MacOS 雪 Leopard 上尝试安装软件的任何软件的一致性问题。

请确保退出 iPhone 模拟器，然后重试安装。

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>已用完类型为0的 trampolines

如果在运行设备时收到此消息，则可以通过修改项目选项 "iPhone Build" 部分来创建更多类型 0 trampolines （类型特定）。  要为设备生成目标添加额外参数：

 `-aot "ntrampolines=2048"`

Trampolines 的默认数目为1024。 尝试增加此数量，直到你的应用程序已足够。

## <a name="ran-out-of-trampolines-of-type-1"></a>已用完类型为1的 trampolines

如果大量使用递归泛型，则可能会在设备上收到此消息。  可以通过修改项目选项 "iPhone Build" 部分来创建更多类型 1 trampolines （类型 RGCTX）。  要为设备生成目标添加额外参数：

 `-aot "nrgctx-trampolines=2048"`

Trampolines 的默认数目为1024。 请尝试增加此数量，直到你已有足够的使用泛型。

## <a name="ran-out-of-trampolines-of-type-2"></a>已用完类型为2的 trampolines

如果使用接口，则可能会在设备上收到此消息。
可以通过修改项目选项 "iPhone Build" 部分来创建更多类型 2 trampolines （类型 IMT Thunk）。  要为设备生成目标添加额外参数：

 `-aot "nimt-trampolines=512"`

IMT Thunk trampolines 的默认数目为128。 请尝试增加此数量，直到你使用了接口。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>调试器无法与设备连接

开始调试设备配置时，会看到调试器显示一个对话框，指示它正在尝试连接到应用程序。 调试器可能无法连接到应用程序的原因有多种，具体取决于连接的模式（USB 或 WiFi）。

 **如果设备和调试器主机位于不同网络上**，则防火墙或专用网络可能会阻止应用程序在 WiFi 模式下连接到调试器主机。

 **Visual Studio for Mac 可能无法查询主机的正确 IP**。 在 WiFi 模式下 Visual Studio for Mac 向应用程序提供它可以找到的主机的所有 Ip，应用程序会尝试所有这些 Ip 来确定是否可以使用这些 Ip 来连接到 Visual Studio for Mac。

 **另一台设备连接到主机上的 USB 端口。** 在少数情况下，连接到主机上的 USB 端口的其他设备被识别为在某种程度上干扰 USB 模式下的调试。

如果 WiFi 或 USB 模式不起作用，则可以轻松尝试其他模式：在 Visual Studio for Mac 中，打开首选项，转到 "首选项"/"调试器/iPhone 调试器" 页，并切换 "通过 WiFi 而不是通过 USB 调试 iOS 设备" 复选框。   如果两者都不起作用，则可以在详细模式下查看有关设备控制台中故障的详细信息（通过将 "-v-v" 添加到项目选项中的其他 mtouch 参数启用）。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>错误134： mtouch 失败，出现以下消息：

如果尝试在 Xamarin 1.4 版的版本上生成 nolink，则可能会引发此错误。 可以通过在 monodevelop 项目配置中指定额外参数来解决此错误。

添加参数

 `-nosymbolstrip`

并且应该解决该问题。

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>分发标识不显示在 Visual Studio for Mac 项目签名选项中

Visual Studio for Mac 2.2 有一个 bug，该 bug 导致无法检测包含逗号的分发证书。 请更新到 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>上传期间出现错误 "AFCFileRefWrite 返回： 1"

将应用上传到设备时，可能会收到错误 "AFCFileRefWrite 返回： 1"。 如果你有一个长度为零的文件，则可能会发生这种情况。

## <a name="error-mtouch-failed-with-no-output"></a>错误 "mtouch 失败，无输出"

当存储解决方案或项目的项目名称或目录包含空格时，Xamarin 和 Visual Studio for Mac 的当前版本会失败。
修复此问题的方法：

- 确保你的项目或其中存储它的目录都包含空格。
- 在项目 "主要设置" 中，确保项目名称不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>错误：上传的二进制文件无效。 SDK 的预发布 beta 版本用于生成应用程序 "

此错误通常是由于在2.0.0 发布之前在 iPad 开发中启动的项目引起的，你可能在信息中有一些密钥。 info.plist，如：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

应删除此密钥，因为 Visual Studio for Mac 会自动对其进行处理。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>错误 "用于构建应用的 SDK 的预发布 beta 版本

（由 Ed Anuff 提供）

执行以下步骤:

- 将 iPhone Build 中的 SDK 版本更改为3.2 或 iTunes connect 将在上传时拒绝它，因为它看到使用小于3.2 的 SDK 版本生成的 iPad 兼容的应用
- 为项目创建自定义 info.plist，并在其中将 MinimumOSVersion 显式设置为3.0。   这将覆盖 Xamarin 设置的 MinimumOSVersion 3.2 值。   如果未执行此操作，应用将无法在 iPhone 上运行。
- 重新生成、压缩并上传到 iTunes connect。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未处理的异常： someSelector：在 {type} 上找不到选择器

此异常由以下三种情况之一引起：

1. 你向目标 C 运行时提供了一个选择器，而未将相应的 [Export] 特性应用于方法
1. 已启用完全链接，但未将 [Preserve] 属性应用于 [Export] ed 方法。
1. 已将 [Export] 特性应用于继承类型中的私有方法。

## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>未更新 MainWindow.xib.designer.cs 文件

Xamarin Studio 2.4 中存在 bug，导致它不会将 xib 文件与新项目中的 Mainwindow.xaml 文件组合在一起。 这意味着它不会更新该特定文件的设计器代码。

此问题已在内置更新程序中提供的 Visual Studio for Mac 版本中得到解决，因此请确保使用较新版本。

可以通过删除（而不是删除） xib 及其设计器文件，然后再将其添加回去来修复现有项目。 这应该会正确地对文件进行分组。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView 或 UIActionSheet 在创建后消失

如果你的代码如下所示：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

"actionSheet" 对象作为函数中的临时变量存在，一旦函数终止，该对象就有资格进行垃圾回收，因此最终会进行垃圾回收。

若要解决此问题，需要保留对方法之外的 "actionSheet" 的引用，该引用将位于方法之外。

## <a name="project-always-runs-in-the-ipad-simulator"></a>项目始终在 iPad 模拟器中运行

IPhone SDK 4.0 安装程序安装了2个 Sdk-3.2 SDK，用于构建仅限 iPad 的应用和 4.0 SDK，用于生成 iPhone 和通用应用。 它还会安装一个3.2 模拟器，该模拟器仅模拟 iPad 和模拟 iPhone 或 iPhone 4 的4.0 模拟器。 删除所有旧版本的 Sdk 和模拟器。

Visual Studio for Mac iPhone 项目生成选项包含用于构建应用的 SDK 版本的设置。 此设置可在**项目选项-> 生成 > IPhone 版本**中找到。

Visual Studio for Mac 中的新项目使用最早安装的 SDK 作为其默认 SDK 设置，如果指定的 SDK 不存在，Visual Studio for Mac 将使用最接近的版本来构建你的应用。 这样做是为了使项目不会始终需要最新的 SDK。 但是，这当前会导致使用 3.2 SDK，这会导致使用 iPad 模拟器。

若要使用 4.0 SDK 修复此问题，请转到**项目选项-> 生成 > IPhone 版本**> 并使用下拉框将 SDK 值更改为 "4.0"。 您必须为每个配置和平台组合执行此操作，并使用面板顶部的下拉列表进行访问。

SDK 版本不应与 "最低操作系统版本" 设置混淆。
此值不必与 SDK 版本值匹配-它会影响你的应用程序将安装在其上的操作系统的最低版本（可以比 SDK 更早的版本），前提是你只使用了旧版本中的 Api，或者使用运行时操作系统版本签来保护新功能的使用。ks. 你应将其设置为在其上测试你的应用程序的最早的 OS 版本。

另请注意，**项目 > IPhone 模拟器目标**> 菜单可用于选择运行/调试项目时默认使用的模拟器。 此外，**运行 > 使用**> "菜单运行，可用于选取要运行的特定模拟器

## <a name="ibtool-returns-error-133"></a>ibtool 返回错误133

这意味着你已安装 XCode 4。   在 XCode 4 中，删除了工具 ibtool 后，就不能再使用独立工具编辑 XIB 文件。

如果要使用 Interface Builder，请从 Apple 网站上安装 XCode 系列3。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>"无法为 mime 类型创建显示绑定： application/vnd.apple.mpegurl. apple-interface-builder"

如果尝试从非 iPhone 项目创建 iPhone UI，会发生此错误。 请确保你开始使用 iPhone/iPad 解决方案，但不能只是将 iPhone UI 元素添加到非 iPhone/iPad 项目。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>在 iOS 模拟器内执行时启动崩溃

如果在模拟器内获取运行时崩溃（SIGSEGV）以及如下所示的堆栈跟踪：

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

...那么，你可能会在模拟器应用程序目录中具有一个（或多个）过时的程序集。 此类程序集可能存在，因为 Apple iOS 模拟器添加并更新文件，但不会删除它们。 如果发生这种情况，最简单的解决方法是选择 "重置和内容和设置 ..."从模拟器菜单。   

> [!WARNING]
> 这会从模拟器中删除所有文件、应用程序和数据。   下一次执行应用程序时，Visual Studio for Mac 会将其部署到模拟器，并且没有旧的过时程序集可导致崩溃。

## <a name="simulator-hangs-during-application-installation"></a>在应用程序安装过程中模拟器挂起

当应用程序名称包含 "." 时，可能会发生这种情况。的名称。
这在 CFBundleExecutable 中被禁止作为可执行文件名称，即使它可以在许多其他情况（如设备）中运行。

"此值不应包含名称上的任何扩展名。"

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>错误：双击 xib 文件时，不支持 "自定义属性类型 0x43"

这是由于未正确设置环境变量时尝试打开 xib 文件引起的。 Visual Studio for Mac/Xamarin 的正常使用情况下不会发生这种情况，并且从/Applications 重新打开 Visual Studio for Mac 应能解决该问题。

尝试更新软件时出现此错误消息，请发送电子邮件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>应用程序在模拟器上运行，但在设备上失败

此问题可能以多种形式出现，并且不会始终产生一致的错误。 如果应用程序包含 xib，请检查以确保 xib 上的**生成操作**设置为**InterfaceDefinition**。 这是 xib 的默认生成操作。

若要检查生成操作，请右键单击 "xib" 文件，然后选择 "**生成操作**"。

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>NotSupportedException：没有可用于编码437的数据

当在你的 Xamarin iOS 应用中包括第三方库时，尝试编译和运行该应用时，你可能会收到 "NotSupportedException：无数据可用于编码 437" 形式的错误。 例如，在操作过程中，库（如 `Ionic.Zip.ZipFile`）可能会引发此异常。

为此，可以打开 Xamarin 项目的选项，转到 " **Ios 生成** > **国际化**" 并查看 "**西部**" 国际化。
