---
title: xib 在 Xamarin 中生成代码
description: 本文档介绍了 Xamarin iOS 如何生成代码以将 xib 文件映射到C#，使可视控件以编程方式进行访问。
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f6218977e9ad0d4c396ef127c3c3ca53dc56d7d3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912568"
---
# <a name="xib-code-generation-in-xamarinios"></a>xib 在 Xamarin 中生成代码

> [!IMPORTANT]
> 本文档说明 Visual Studio for Mac 仅与 Xcode 的 Interface Builder 集成，因为 Xamarin Designer for iOS 未使用操作和插座。 有关 iOS 设计器的详细信息，请查看[Ios 设计器](~/ios/user-interface/designer/index.md)文档。

Apple Interface Builder 工具（"IB"）可用于直观地设计用户界面。 IB 创建的接口定义保存在**xib**文件中。 可能会为**xib**文件中的小组件和其他对象提供 "类标识"，该标识可以是自定义的用户定义类型。 这允许您自定义小组件的行为和编写自定义小组件。

这些用户类通常是 UI 控制器类的子类。 它们具有可连接到接口对象的*插座*（类似于属性）和*操作*（类似于事件）。 在运行时，加载 IB 文件时，会创建对象，并将插座和操作动态连接到各种 UI 对象。 定义这些托管类时，必须定义所有操作和插座，使其与 IB 预期的所有操作和插座匹配。 Visual Studio for Mac 使用类似代码隐藏的模型来简化此类。 这类似于 Xcode 对调整的作用，但代码生成模型和约定更熟悉 .NET 开发人员。

在 Visual Studio 中，目前不支持使用**xib**文件。

## <a name="xib-files-and-custom-classes"></a>xib 文件和自定义类

还可以在**xib**文件中定义自定义类型，也可以使用 Cocoa 触控中的现有类型。 还可以使用其他**xib**文件中定义的类型，或者使用纯粹在代码中C#定义的类型。 目前 Interface Builder 不知道在**xib**文件外部定义的类型的详细信息，因此不会列出它们，也不会显示其自定义插座和操作。 删除此限制计划在将来的某个时间进行。

可使用 Interface Builder 的 "类" 选项卡中的 "添加子类" 命令在**xib**文件中定义自定义类。 我们将它们称为 "代码隐藏" 类。 如果**xib**文件的项目中有一个 "xib.designer.cs" 对应文件，则 Visual Studio for Mac 会自动使用**xib**中所有自定义类的分部类定义来填充它。 我们将这些分部类称为 "设计器类"。

## <a name="generating-code"></a>生成代码

对于具有 "生成操作"*页*的任何 **{0}xib**文件，如果项目中也存在 **{0}xib.designer.cs**文件，则 Visual Studio for Mac 将在设计器文件中为其可在**xib**文件中找到的所有用户类生成分部类，并为所有操作的插座和分部方法提供属性。 仅当存在此文件时，才会启用代码生成。

当**xib**文件更改并 Visual Studio for Mac 重新获得焦点时，将自动更新该设计器文件。 不应手动修改设计器文件，因为下次 Visual Studio for Mac 更新文件时将覆盖更改。

## <a name="registration-and-namespaces"></a>注册和命名空间

Visual Studio for Mac 使用设计器文件位置的项目默认命名空间生成设计器类，以使其与普通 .NET 项目 namespacing 保持一致。 设计器文件的命名空间由项目的 "默认命名空间" 及其 ".NET 命名策略" 设置驱动。 请注意，如果项目的默认命名空间发生更改，MD 将重新生成新命名空间中的类，因此你可能会发现分部类不再匹配。

为了使此类可由目标-C 运行时发现，Visual Studio for Mac 将 `[Register (name)]` 特性应用于类。 虽然 Xamarin 会自动注册 `NSObject`派生的类，但它使用完全限定的 .NET 名称。 Visual Studio for Mac 应用的特性重写此，以确保每个类都注册到**xib**文件中使用的名称。 如果在 IB 中使用自定义类，而不使用 Visual Studio for Mac 生成设计器文件，则可能需要手动应用此类，以使托管类与预期的目标 C 类名称匹配。

不能在多个 xib 中定义类 **。**

## <a name="non-designer-class-parts"></a>非设计器类部件

设计器分部类不应按原样使用。 插座是专用的，不指定基类。 预计每个类都将在另一个文件中具有相应的 "非设计器" 类部件，这会设置基类、使用或公开插座，并定义在加载**xib**时实例化类所需的构造函数。 **Xib**模板执行此操作，但对于在**xib**中定义的任何其他自定义类，必须手动添加非设计器部分。

导致这种情况的原因是需要灵活性。 例如，多个代码隐藏类可以为公共托管抽象类提供子类，此类将类划分为通过 IB 划分子类。

将其放在 xib.designer.cs 设计器文件 **{0}** 旁边的 **{0}xib.cs**文件中。

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>生成的操作和插座

在分部设计器类中，Visual Studio for Mac 生成对应于 IB 中定义的任何连接的插座的属性，以及与任何连接操作对应的分部方法。

### <a name="outlet-properties"></a>插座属性

设计器类包含与自定义类上定义的所有插座相对应的属性。 这些是属性的一个事实是，用于启用延迟绑定的 Xamarin 到目标 C 桥的实现细节。 应将它们视为等效于私有字段，以便仅用于代码隐藏类。 如果要使它们成为公共的，请将访问器属性添加到非设计器类部件，就像对任何其他私有字段一样。

如果将插座属性定义为具有一种类型的 `id` （等效于 `NSObject`），则设计器代码生成器当前根据连接到该插座的对象确定最强的可能类型。
但是，在将来的版本中可能不支持这种情况，因此，建议您在定义自定义类时明确地键入插座。

### <a name="action-properties"></a>操作属性

设计器类包含与自定义类上定义的所有操作对应的分部方法。 这些是没有实现的方法。 分部方法的目的是双重的：

1. 如果在非设计器类部件的类体中键入 `partial`，Visual Studio for Mac 将自动完成所有未实现的分部方法的签名。
2. 分部方法签名具有应用的属性，可将这些属性公开给目标-C 环境，因此，它们可以作为对应的操作进行处理。

如果需要，您可以忽略分部方法，并通过将特性应用于其他方法来实现该操作，也可以让其通过基类。

如果将操作定义为具有 `id` 的发送方类型（相当于 `NSObject`），则设计器代码生成器当前根据连接到该操作的对象确定最强的可能类型。 但是，在将来的版本中可能不支持这种情况，因此，建议您在定义自定义类时显式地键入操作。

请注意，只为C#创建这些分部方法，因为 CodeDOM 不支持分部方法，因此不会为其他语言生成这些方法。

## <a name="cross-xib-class-usage"></a>跨 XIB 类用法

有时，用户希望从多个**xib**文件引用相同的类（例如，具有选项卡控制器）。 为此，可以从另一个**xib**文件显式引用类定义，或者在**xib**中再次定义相同的类名。

后一种情况可能会导致 xib 文件单独 Visual Studio for Mac 处理 **。** 它无法自动检测和合并重复的定义，因此，如果在多个设计器文件中定义同一个分部类，则可能会发生冲突，多次应用 Register 特性。 最新版本的 Visual Studio for Mac 尝试解决此问题，但它可能不会始终按预期方式工作。 将来，这可能会变得不受支持，而 Visual Studio for Mac 会使所有**xib**文件中的所有类型和项目中的托管代码直接在**xib**文件中可见。

## <a name="type-resolution"></a>类型解析

IB 中使用的类型为目标-C 类型名称。 这些类型通过使用 Register 特性映射到 CLR 类型。 生成插座和操作代码时，Visual Studio for Mac 将为 Xamarin core 所包装的所有目标 C 类型解析相应的 CLR 类型，并完全限定其类型名称。

但是，代码生成器目前无法解析用户代码或库中的目标 C 类型名称中的 CLR 类型，因此在这种情况下，它将输出类型名称原义。 这意味着相应的 CLR 类型必须与目标 C 类型具有相同的名称，并且与使用该类型的代码位于同一命名空间中。 在代码生成过程中，通过考虑项目中的所有目标 C 类型，计划在将来某个时间修复这种情况。
