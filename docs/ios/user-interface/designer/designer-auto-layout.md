---
title: 用 Xamarin Designer for iOS 自动布局
description: 本指南介绍 iOS 自动布局，并说明如何使用 Xamarin Designer for iOS 创建和编辑使用约束的布局。 它还讨论了如何在代码中修改约束、对约束更改进行动画处理等。
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 35a8d3aeb00ac73f944712cb31f913f98bd3b6e8
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78919157"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>用 Xamarin Designer for iOS 自动布局

自动布局（也称为 "自适应布局"）是一种响应式设计方法。 与过渡布局系统不同，其中每个元素的位置均硬编码为屏幕上的某个点，自动布局是关于*关系*的，即相对于设计图面上的其他元素的元素位置。 自动布局的核心是约束或规则，用于定义元素或一组元素在屏幕上其他元素的上下文中的位置。 由于元素未绑定到屏幕上的特定位置，因此约束有助于创建在不同屏幕大小和设备方向上看起来良好的自适应布局。

在本指南中，我们将介绍约束以及如何在 Xamarin iOS 设计器中使用它们。 本指南不涉及以编程方式使用约束。 有关以编程方式使用自动布局的信息，请参阅[Apple 文档](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>要求

在 Visual Studio 2017 和更高版本的 Windows 中 Visual Studio for Mac 提供 Xamarin Designer for iOS。

本指南假定在[IOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南中了解设计器的组件。

## <a name="introduction-to-constraints"></a>约束简介

约束是屏幕上两个元素之间的关系的数学表示形式。 将 UI 元素的位置表示为数学关系，解决了与硬编码 UI 元素的位置相关的几个问题。 例如，如果要在纵向模式下在屏幕底部放置按钮20px，按钮的位置将在横向模式下关闭。 为避免出现这种情况，我们可以设置一个约束，将20px 按钮的下边缘放置在视图的底部。 然后，按钮边缘的位置将计算为*按钮。底端 = 20px*，这会在纵向和横向模式下将按钮从视图的底部20px。 根据数学关系计算位置的功能是使约束在 UI 设计中非常有用。

当我们设置约束时，我们将创建一个 `NSLayoutConstraint` 对象，该对象将所约束的对象作为参数，并使用该约束将作用于的属性或*特性*。 在 iOS 设计器中，属性包括边缘，如元素的*左*、*右*、*上*和*下*。 它们还包括大小属性（如*高度*和*宽度*）以及中心点位置、 *system.windows.media.rotatetransform.centerx*和*system.windows.media.rotatetransform.centery*。 例如，当我们在两个按钮的左边缘的位置添加约束时，设计器将在以下代码段中生成以下代码：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一部分介绍如何使用 iOS 设计器处理约束，包括启用和禁用自动布局以及使用约束工具栏。

## <a name="enable-auto-layout"></a>启用自动布局

默认的 iOS 设计器配置启用了约束模式。 但是，如果您需要手动启用或禁用它，则可以执行两个步骤：

1. 单击设计图面上的空白区域。 这会取消选择所有元素并显示情节提要文档的属性。
1. 选中或取消选中 "属性" 面板中的 "**使用自动布局**" 复选框：

    ![](designer-auto-layout-images/image01.png "The Use Autolayout checkbox in the property panel")

默认情况下，不在图面上创建或显示任何约束。 相反，它们会在编译时从帧信息自动推断。 若要添加约束，需要在设计图面上选择一个元素并向其添加约束。 可以使用**约束工具栏**执行此操作。

## <a name="constraints-toolbar"></a>约束工具栏

 [![](designer-auto-layout-images/toolbarnew.png "The Context Menu Commands")](designer-auto-layout-images/toolbarnew.png#lightbox)

约束工具栏已更新，现在包含两个主要部分：

- **约束模式按钮切换**：以前，你可以通过再次单击设计图面上的所选视图来进入约束模式。 现在应在约束栏中使用此切换按钮：

  ![约束模式切换](designer-auto-layout-images/constraints.png)

- **"更新约束" 按钮：** 需要注意的是，根据是否处于约束编辑模式来进行更改。
  - 在 "约束编辑模式" 中，此按钮调整约束以匹配元素框架。
  - 在框架编辑模式下，此按钮调整元素框以匹配约束的定义位置。

## <a name="constraints-editing-popover"></a>Segue 编辑约束

使用 "约束编辑器" 弹出窗口，我们可以为选择视图添加和更新多个约束。 可以创建多个间距、纵横比和对齐约束，如将视图与两个视图的左边缘对齐。

若要编辑所选视图的约束，请单击省略号以显示 segue： ![约束编辑 segue](designer-auto-layout-images/constraints-popup.png)

打开约束后，segue 会在视图上显示任何预设约束。 我们可以在右上角选择组合框中的所有**边**，然后选择 "**全部清除**" 将其删除。

**W**将设置 Width 和**H**来设置 height 约束。 检查**纵横比**时，将在不同的屏幕大小上控制视图的高度和宽度，视图的宽度用作 r 的分子，高度为分母。

![约束间距](designer-auto-layout-images/constraints-spacing.png)

间距约束的四个组合框列出了用于定位约束的相邻视图

## <a name="surface-based-constraint-editing"></a>基于面的约束编辑

若要进行更精细的约束编辑，可以直接在设计图面上与约束交互。 本部分介绍了基于图面的约束编辑的基础知识，其中包括旋转间距控件、拖放区域和使用不同类型的约束。

### <a name="creating-constraints"></a>创建约束

IOS 设计器工具提供了两种类型的控件用于在设计图面上操作元素。 *拖动控件*和*固定间距控件*，如下图所示：

![查看控件](designer-auto-layout-images/controls.png)

这些都是通过选择约束栏中的约束模式按钮来切换的。

元素的每一侧的4个 T 形控点为约束定义元素的*上*、*右*、*下*和*左边*缘。 元素右侧和底部的两个 I 形控点分别定义*高度*和*宽度*约束。 中间正方形同时处理*system.windows.media.rotatetransform.centerx*和*system.windows.media.rotatetransform.centery*约束。

若要创建约束，请选取一个句柄并将其拖到设计图面上的某个位置。 当你开始拖动时，将在表面上显示一系列绿色线条/框，告诉你你可以限制哪些内容。 例如，在下面的屏幕截图中，我们将约束中间按钮的顶部：

 [![](designer-auto-layout-images/image07.png "Constraining the top side of the middle button")](designer-auto-layout-images/image07.png#lightbox)

请注意两个按钮之间的三个绿色虚线。 绿线指示*放置区*，或可以约束的其他元素的特性。 在上面的屏幕截图中，另外两个按钮提供3个垂直的拖放区域（*底部*、 *system.windows.media.rotatetransform.centery*、*顶部*），以限制我们的按钮。 视图顶部的虚线绿色线条表示视图控制器在视图顶部提供了一个约束，而绿色的绿色框表示视图控制器在顶部布局参考线下面提供了一个约束。

> [!IMPORTANT]
> 布局参考线是特定类型的约束目标，使我们能够创建将考虑系统条形（如状态栏或工具栏）的上限和下限。 其中一项主要用途是在 iOS 6 和 iOS 7 之间具有兼容的应用程序，因为最新版本的容器视图在状态栏下扩展。 有关顶部布局指南的详细信息，请参阅[Apple 文档](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。

接下来的三个部分介绍了如何使用不同类型的约束。

### <a name="size-constraints"></a>大小限制

大小限制-*高度*和*宽度*-有两个选项。 第一种方法是拖动句柄以约束到相邻元素大小，如上述示例所示。 另一种方法是双击句柄以创建自约束。 这样，我们就可以指定常量大小值，如以下屏幕截图所示：

 [![](designer-auto-layout-images/sizec.png "Drag the handle to constrain to a neighbor element size, as illustrated here")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>中心约束

正方形控点将根据上下文创建*system.windows.media.rotatetransform.centerx*或*system.windows.media.rotatetransform.centery*约束。 拖动正方形控点会使其他元素亮起，以提供垂直和水平拖放区域，如以下屏幕截图所示：

 [![](designer-auto-layout-images/centerc.png "Center Constraints")](designer-auto-layout-images/centerc.png#lightbox)

如果选择垂直拖放区域，则将创建*system.windows.media.rotatetransform.centery*约束。 如果选择水平放置区，则约束将基于*system.windows.media.rotatetransform.centerx*。

### <a name="combinational-constraints"></a>Combinational 约束

若要在两个元素之间创建对齐和大小相等约束，您可以从顶部工具栏选择项以指定-按顺序水平对齐、垂直对齐和大小 equalities，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image06.png "Combinational Constraints")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>可视化和编辑约束

当您添加约束时，它将在您选择项时显示在设计图面上：

 [![](designer-auto-layout-images/image09.png "Visualizing Constraints")](designer-auto-layout-images/image09.png#lightbox)

您可以通过单击一条蓝线并直接在属性面板中编辑约束值来选择约束。 或者，双击蓝线会显示一个 segue，您可以直接在设计图面上编辑这些值：

 [![](designer-auto-layout-images/image08.png "Editing Constraints")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>约束问题

使用约束时可能会出现几种类型的问题：

- **冲突约束**-当多个约束强制元素具有属性的冲突值并且约束引擎无法协调它们时，会发生这种情况。
- **Underconstrained 项**—元素的属性（位置 + 大小）必须完全由其约束集和内部大小覆盖，约束才能有效。 如果这些值不明确，则称为 underconstrained。
- **Frame misplacement** -当元素的帧及其一组约束定义两个不同的结果矩形时，会发生这种情况。

本部分然后详细阐述上面列出的三个问题，并提供有关如何处理它们的详细信息。

### <a name="conflicting-constraints"></a>冲突约束

冲突的约束标为红色，并且具有警告符号。 如果将鼠标悬停在警告符号上，将显示一个 segue，其中包含有关冲突的信息：

 [![](designer-auto-layout-images/image11.png "Conflicting Constraints warning")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 项

Underconstrained 项以橙色显示，并触发视图控制器对象栏中橙色标记图标的外观：

 [![](designer-auto-layout-images/image02.png "Underconstrained items appear in orange")](designer-auto-layout-images/image02.png#lightbox)

如果单击该标记图标，则可以获取有关场景中 underconstrained 项的信息，并通过完全约束这些问题或删除其约束来解决问题，如以下屏幕截图所示：

 [![](designer-auto-layout-images/image10.png "Fixing Underconstrained Items")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>帧 Misplacement

Frame misplacement 使用与 underconstrained 项相同的颜色代码。 该项目将始终使用其本机帧在表面上呈现，但对于帧 misplacement，红色矩形会标记在应用程序运行时项目的最终位置，如以下屏幕截图所示：

 [![](designer-auto-layout-images/image05.png "Sample Frame Misplacement view")](designer-auto-layout-images/image05.png#lightbox)

若要解决帧 misplacement 错误，请从 "约束" 工具栏（最右侧的按钮）中选择 "**基于约束更新帧**" 按钮：

 [![](designer-auto-layout-images/image03.png "Update Frames based on Constraints toolbar button")](designer-auto-layout-images/image03.png#lightbox)

这将自动调整元素框以匹配控件定义的位置。

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>修改代码中的约束

根据应用程序的要求，有时需要在代码中修改约束。 例如，若要调整约束附加到的视图的大小或重新定位，则更改约束的优先级或完全停用约束。

若要在代码中访问约束，必须先执行以下操作，在 iOS 设计器中公开该约束：

1. 以 normal 形式创建约束（使用以上列出的方法）。
2. 在 "**文档大纲资源管理器**" 中，找到所需的约束并将其选中：

    [![](designer-auto-layout-images/modify01.png "The Document Outline Explorer")](designer-auto-layout-images/modify01.png#lightbox)
3. 接下来，在 "**属性资源管理器**" 的 "**小组件**" 选项卡中为约束指定一个**名称**：

    [![](designer-auto-layout-images/modify02.png "The Widget Tab")](designer-auto-layout-images/modify02.png#lightbox)
4. 保存所做更改。

在进行上述更改后，你可以在代码中访问约束并修改其属性。 例如，可以使用以下项将附加视图的高度设置为零：

```csharp
ViewInfoHeight.Constant = 0;
```

在 iOS 设计器中给定约束的以下设置：

[![](designer-auto-layout-images/modify03.png "Editing a Constraint in the Property Explorer")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延迟的布局传递

自动布局引擎不会立即更新附加视图以响应约束更改，而是在不久的将来计划_延迟布局处理过程_。 在此延迟传递过程中，不仅会更新给定视图的约束，还会重新计算和更新层次结构中每个视图的约束，以调整新布局。

在任何时候，都可以通过调用父视图的 `SetNeedsLayout` 或 `SetNeedsUpdateConstraints` 方法来计划自己的延迟布局传递。

延迟的布局传递通过视图层次结构包含两个唯一的传递：

- **更新通过**此传递，自动布局引擎遍历视图层次结构，并对所有视图控制器调用 `UpdateViewConstraints` 方法，并对所有视图调用 `UpdateConstraints` 方法。
- 此布局将再次**传递**，自动布局引擎将遍历视图层次结构，但这次将对所有视图控制器调用 `ViewWillLayoutSubviews` 方法，并对所有视图调用 `LayoutSubviews` 方法。 `LayoutSubviews` 方法使用自动布局引擎计算的矩形更新每个子视图的 `Frame` 属性。

### <a name="animating-constraint-changes"></a>对约束更改进行动画处理

除了修改约束属性，还可以使用核心动画对视图约束的更改进行动画处理。 例如：

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

此处的关键是在动画块中调用父视图的 `LayoutIfNeeded` 方法。 这会告知视图绘制动态位置或大小变化的每个 "帧"。 如果没有此行，视图只会与最终版本对齐，而不进行动画处理。

## <a name="summary"></a>总结

本指南介绍了 iOS 自动（或 "自适应"）布局和约束概念，作为设计图面上元素间关系的数学表示形式。 它介绍了如何在 iOS 设计器中启用自动布局、使用 "**约束" 工具栏**以及在设计图面上分别编辑约束。 接下来，它介绍了如何排查三个常见的约束问题。 最后，它演示了如何在代码中修改约束。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 设计控制演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android Designer 概述](~/android/user-interface/android-designer/index.md)
- [编程约束](~/ios/user-interface/programmatic-layout-constraints.md)
