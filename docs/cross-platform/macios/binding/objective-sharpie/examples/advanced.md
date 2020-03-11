---
title: 高级（手动）实际示例
description: 本文档介绍了如何使用 xcodebuild 的输出作为客观 Sharpie 的输入，从而深入了解目标 Sharpie 的作用。
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 2dea16633181d6b1120a5f9a90da685df66e5451
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79031012"
---
# <a name="advanced-manual-real-world-example"></a>高级（手动）实际示例

**此示例使用[Facebook 中的 POP 库](https://github.com/facebook/pop)。**

本部分介绍更高级的绑定方法，在此方法中，我们将使用 Apple 的 `xcodebuild` 工具先生成 POP 项目，然后手动推导目标 Sharpie 的输入。 实质上，这涉及到在上一部分中 Sharpie 的目标。

```bash
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

由于 POP 库有 Xcode 项目（`pop.xcodeproj`），因此，只需使用 `xcodebuild` 生成 POP 即可。 此过程可能会反过来生成目标 Sharpie 可能需要分析的标头文件。 这就是在绑定之前生成很重要的原因。 当通过 `xcodebuild` 生成时，请确保传递要传递到目标 Sharpie 的相同 SDK 标识符和体系结构（请记住，客观 Sharpie 3.0 通常会为你执行此操作！）：

```bash
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1

=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===

...

CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP

...

** BUILD SUCCEEDED **
```

控制台中将有大量的生成信息输出作为 `xcodebuild`的一部分。 如上所示，我们可以看到 "CpHeader" 目标已运行，其中标头文件已复制到生成输出目录。 这种情况通常是一种情况，并使绑定更容易：作为本机库的生成的一部分，头文件通常会复制到 "公开" 的可耗用位置，这可使分析更易于绑定。 在这种情况下，我们知道 POP 的标头文件位于 `build/Headers` 目录中。

现已准备好绑定 POP。 我们知道我们要为 SDK `iphoneos8.1` 提供 `arm64` 体系结构，并且我们关注的标头文件位于 POP git 结帐下的 `build/Headers` 中。 如果我们查找 `build/Headers` 目录中，将看到大量的标头文件：

```bash
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

如果查看 `POP.h`，则可以看到它是库的主顶级头文件，它 `#import`其他文件。 因此，只需将 `POP.h` 传递给目标 Sharpie，clang 将在幕后执行 rest 操作：

```bash
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

你会注意到，我们向目标 Sharpie 传递了一个 `-scope build/Headers` 参数。 由于 C 和目标-C 库必须 `#import` 或 `#include` 作为库的实现细节的其他头文件（而不是要绑定的 API），因此 `-scope` 参数告诉客观 Sharpie 忽略未在 `-scope` 目录内某个文件中定义的任何 API。

对于完全实现的库，你会发现 `-scope` 参数通常是可选的，但在显式提供此参数时，不会造成任何伤害。 

> [!TIP]
> 如果库的标头导入任何 iOS SDK 标头（例如 `#import <Foundation.h>`），则需要设置作用域，否则客观 Sharpie 将为导入的 iOS SDK 标头生成绑定定义，从而导致在编译绑定项目时可能会生成错误的大绑定。 

此外，我们还指定 `-c -Ibuild/headers`。 首先，`-c` 参数告诉客观 Sharpie 停止解释命令行参数并直接将任何后续参数传递_到 clang 编译器_。 因此，`-Ibuild/Headers` 是一个 clang 编译器参数，该参数指示 clang 在 "`build/Headers`" 下搜索包含位置，这是 POP 标题的实际位置。 如果没有此参数，clang 将不知道在何处查找 `POP.h` `#import`的文件。 _几乎所有的 "问题" 都是使用客观 Sharpie 可归结，来找出要传递到 clang 的内容_。

### <a name="completing-the-binding"></a>完成绑定

目标 Sharpie 现已生成 `Binding/ApiDefinitions.cs` 和 `Binding/StructsAndEnums.cs` 文件。

它们是目标 Sharpie 在绑定上的基本第一次传递，在少数情况下，可能只需要。 如前文所述，开发人员通常需要在客观 Sharpie 完成后手动修改生成的文件，以解决无法由该工具自动处理的[任何问题](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)。

更新完成后，现在可以将这两个文件添加到 Visual Studio for Mac 中的绑定项目，也可以直接传递到 `btouch` 或 `bmac` 工具来生成最终绑定。

有关绑定过程的详细说明，请参阅我们的[完整演练说明](~/ios/platform/binding-objective-c/walkthrough.md)。
