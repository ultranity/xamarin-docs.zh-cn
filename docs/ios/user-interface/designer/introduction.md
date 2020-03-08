---
title: iOS 设计器基础知识
description: 本指南介绍 Xamarin Designer for iOS。 它演示了如何使用 iOS 设计器以可视方式布局控件，如何在代码中访问这些控件，以及如何编辑属性。
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/31/2018
ms.openlocfilehash: 6b02a0f8476cf47ca6df279653095fe0845b36c9
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914826"
---
# <a name="ios-designer-basics"></a>iOS 设计器基础知识

本指南 _介绍 Xamarin Designer for iOS。它演示了如何使用 iOS 设计器以可视方式布局控件，如何在代码中访问这些控件，以及如何编辑属性。_

Xamarin Designer for iOS 是类似于 Xcode 的 Interface Builder 和 Android Designer 的可视化界面设计器。 其中一些功能包括与 Visual Studio for Windows 和 Mac 的无缝集成、拖放编辑、用于设置事件处理程序的接口，以及用于呈现自定义控件的功能。

## <a name="requirements"></a>要求

IOS 设计器可用于 Visual Studio for Mac 和 Visual Studio 2017 及更高版本的 Windows。 在 Visual Studio for Windows 中，iOS 设计器需要连接到正确配置的 Mac 生成主机，但 Xcode 无需运行。

本指南假定你熟悉[入门指南](~/ios/get-started/index.md)中所述的内容。

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>IOS 设计器的工作方式

本部分介绍 iOS 设计器如何帮助创建用户界面并将其连接到代码。

使用 iOS 设计器，开发人员可直观地设计应用程序的用户界面。 如情节提要指南[简介](~/ios/user-interface/storyboards/index.md)中所述，storyboard 介绍了构成应用的屏幕（视图控制器）、放置在这些视图控制器上的界面元素（视图）以及应用的整个导航流。 

视图控制器有两部分： iOS 设计器中的可视化表示形式和关联C#类：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![IOS 设计器中的视图控制器](introduction-images/1-storyboardwithviewcontroller-vsmac.png "IOS 设计器中的视图控制器")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![视图控制器的代码](introduction-images/2-viewcontrollercode-vsmac.png "视图控制器的代码")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IOS 设计器中的视图控制器](introduction-images/1-storyboardwithviewcontroller-vs.png "IOS 设计器中的视图控制器")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![视图控制器的代码](introduction-images/2-viewcontrollercode-vs.png "视图控制器的代码")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

在默认状态下，视图控制器不提供任何功能;它必须用控件填充。 这些控件放在视图控制器的视图中，其中包含所有屏幕内容的矩形区域。 大多数视图控制器都包含常用控件（如按钮、标签和文本字段），如以下屏幕截图所示，其中显示了一个包含按钮的视图控制器： 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![包含按钮的视图控制器](introduction-images/3-viewcontrollerwithbutton-vsmac.png "包含按钮的视图控制器")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![包含按钮的视图控制器](introduction-images/3-viewcontrollerwithbutton-vs.png "包含按钮的视图控制器")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

某些控件（例如包含静态文本的标签）可以添加到视图控制器，并将其放在单独的控件中。 但通常情况下，必须以编程方式自定义控件。 例如，上面添加的按钮应该在点击时执行某些操作，因此必须在代码中添加事件处理程序。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

为了访问和操作代码中的按钮，该按钮必须具有唯一标识符。 通过选择按钮、打开**Properties Pad**并将其 "**名称**" 字段设置为一个值（如 "SubmitButton"）来提供唯一标识符：

[![在 Properties Pad 中设置按钮的名称](introduction-images/4-settingbuttonname-vsmac.png "在 Properties Pad 中设置按钮的名称")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

为了访问和操作代码中的按钮，该按钮必须具有唯一标识符。 通过选择按钮，打开 "**属性" 窗口**，并将其 "**名称**" 字段设置为一个值（如 "SubmitButton"）来提供唯一标识符：

[![在 "属性" 窗口中设置按钮的名称](introduction-images/4-settingbuttonname-vs.png "在 "属性" 窗口中设置按钮的名称")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

由于按钮具有名称，因此可以在代码中对其进行访问。 但这是如何工作的？

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在**Solution Pad**中，导航到 " **ViewController.cs** " 并单击 "泄露指示器" 会显示视图控制器 `ViewController` 类定义跨两个文件，其中每个文件都包含一个[分部类](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定义：

[![组成 ViewController 类的两个文件：ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "构成 ViewController 类的两个文件：ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在**解决方案资源管理器**中，导航到 " **ViewController.cs** " 并单击 "泄露指示器" 会显示视图控制器 `ViewController` 类定义跨两个文件，其中每个文件都包含一个[分部类](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定义：

[![组成 ViewController 类的两个文件：ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "构成 ViewController 类的两个文件：ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- 应该用与 `ViewController` 类相关的自定义代码来填充**ViewController.cs** 。 在此文件中，`ViewController` 类可响应各种 iOS 视图控制器生命周期方法、自定义 UI 以及响应用户输入（如点击按钮）。

- **ViewController.designer.cs**是一个生成的文件，由 IOS 设计器创建，以将可视化构造的接口映射到代码。 由于对此文件所做的更改将被覆盖，因此不应进行修改。 此文件中的属性声明使得 `ViewController` 类中的代码可以按**名称**访问 IOS 设计器中设置的控件。 打开**ViewController.designer.cs**会显示以下代码：

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

`SubmitButton` 属性声明将整个 `ViewController` 类（而不仅仅是**ViewController.designer.cs**文件）连接到情节提要中定义的按钮。 由于**ViewController.cs**定义了部分 `ViewController` 类，因此它有权访问 `SubmitButton`。

下面的屏幕截图说明，IntelliSense 现在可识别**ViewController.cs**中的 `SubmitButton` 引用：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![IntelliSense 识别 SubmitButton 引用](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense 识别 SubmitButton 引用")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IntelliSense 识别 SubmitButton 引用](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense 识别 SubmitButton 引用")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

本部分演示了如何在 iOS 设计器中创建一个按钮，以及如何在代码中访问该按钮。

本文档的其余部分进一步概述了 iOS 设计器。

## <a name="ios-designer-basics"></a>iOS 设计器基础知识

本部分介绍 iOS 设计器的各个部分，并提供其功能简介。

### <a name="launching-the-ios-designer"></a>启动 iOS 设计器

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

使用 Visual Studio for Mac 创建的 Xamarin iOS 项目包含情节提要。 若要查看情节提要的内容，请双击 " **Solution Pad**中的" storyboard "文件：

[![在 iOS 设计器中打开的演示图板](introduction-images/7-storyboardopen-vsmac.png "在 iOS 设计器中打开的演示图板")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

使用 Visual Studio 创建的大多数 Xamarin 项目都包含情节提要。 若要查看情节提要的内容，请双击 "**解决方案资源管理器**中的" storyboard "文件：

[![在 iOS 设计器中打开的演示图板](introduction-images/7-storyboardopen-vs.png "在 iOS 设计器中打开的演示图板")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>iOS 设计器功能

IOS 设计器有六个主要部分：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![IOS 设计器部分](introduction-images/8-sixpartsofiosdesigner-vsmac.png "IOS 设计器部分")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Design Surface** – IOS 设计器的主工作区。 在文档区域中显示，它支持用户界面的直观构造。
2. **约束工具栏**-允许在框架编辑模式和约束编辑模式间切换，这两种不同的方法可以在用户界面中定位元素。
3. **"工具箱**" –列出可拖放到设计图面并添加到用户界面的控制器、对象、控件、数据视图、笔势识别器、窗口和条。
4. **Properties Pad** –显示选定控件的属性，包括标识、视觉样式、可访问性、布局和行为。
5. **文档大纲**–显示构成所编辑接口的布局的控件树。 单击树中的某一项将在 iOS 设计器中选择它，并在**Properties Pad**中显示其属性。 这对于在深层嵌套的用户界面中选择特定控件非常方便。
6. **底部工具栏**-包含用于更改 IOS 设计器如何显示 xib 文件的选项，包括设备、方向和缩放。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IOS 设计器部分](introduction-images/8-sixpartsofiosdesigner-vs.png "IOS 设计器部分")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Design Surface** – IOS 设计器的主工作区。 在文档区域中显示，它支持用户界面的直观构造。
2. **约束工具栏**-允许在框架编辑模式和约束编辑模式间切换，这两种不同的方法可以在用户界面中定位元素。
3. **"工具箱**" –列出可拖放到设计图面并添加到用户界面的控制器、对象、控件、数据视图、笔势识别器、窗口和条。
4. "**属性" 窗口**–显示选定控件的属性，包括标识、视觉样式、可访问性、布局和行为。
5. **文档大纲**–显示构成所编辑接口的布局的控件树。 单击树中的某一项将在 iOS 设计器中选择该项，然后在 "**属性" 窗口**中显示其属性。 这对于在深层嵌套的用户界面中选择特定控件非常方便。
6. **底部工具栏**-包含用于更改 IOS 设计器如何显示 xib 文件的选项，包括设备、方向和缩放。

-----

### <a name="design-workflow"></a>设计工作流

#### <a name="adding-a-control-to-the-interface"></a>向接口添加控件

若要将控件添加到接口，请将其从 "**工具箱**" 拖放到设计图面上。 添加或定位控件时，垂直和水平指导原则突出显示了常用布局位置，如垂直居中、水平居中和边距：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![在设计图面上，指导原则重点介绍常用的布局位置](introduction-images/9-layoutguides-vsmac.png "在设计图面上，指导原则重点介绍常用的布局位置")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![在设计图面上，指导原则重点介绍常用的布局位置](introduction-images/9-layoutguides-vs.png "在设计图面上，指导原则重点介绍常用的布局位置")

-----

上面示例中的蓝色点线提供了水平居中视觉对象对齐准则，以帮助提供按钮位置。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="context-menu-commands"></a>上下文菜单命令

在设计图面和**文档大纲**中，上下文菜单都可用。 此菜单提供选定控件及其父控件的命令，这在使用嵌套层次结构中的视图时很有用：

[![设计图面上的上下文菜单](introduction-images/10-contextmenudesignsurface-vsmac.png "设计图面上的上下文菜单")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>约束工具栏

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![约束工具栏](introduction-images/11-constraintstoolbar-vsmac.png "约束工具栏")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![约束工具栏](introduction-images/11-constraintstoolbar-vs.png "约束工具栏")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

约束工具栏已更新，现在包含以下两个控件： "框架编辑模式/约束编辑模式切换" 和 "更新约束/更新帧" 按钮。

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>帧编辑模式/约束编辑模式切换

在以前版本的 iOS 设计器中，单击设计图面上已经选定的视图在框架编辑模式和约束编辑模式间切换。 现在，约束工具栏中的切换控件在这些编辑模式间切换。

- 帧编辑模式：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

!["帧编辑模式" 按钮](introduction-images/12a-frameeditingmode-vsmac.png ""帧编辑模式" 按钮")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

!["帧编辑模式" 按钮](introduction-images/12a-frameeditingmode-vs.png ""帧编辑模式" 按钮")

-----

- 约束编辑模式：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

!["约束编辑模式" 按钮](introduction-images/12b-constrainteditingmode-vsmac.png ""约束编辑模式" 按钮")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

!["约束编辑模式" 按钮](introduction-images/12b-constrainteditingmode-vs.png ""约束编辑模式" 按钮")

-----

#### <a name="update-constraints--update-frames-button"></a>"更新约束/更新帧" 按钮

"更新约束/更新帧" 按钮位于 "帧编辑模式/约束编辑模式" 切换到右侧。

- 在框架编辑模式下，单击此按钮可调整任何所选元素的帧，以匹配其约束。
- 在 "约束编辑模式" 中，单击此按钮可调整任何所选元素的约束，使其与框架相匹配。

### <a name="bottom-toolbar"></a>底部工具栏

底部工具栏提供了一种方法，用于选择用于在 iOS 设计器中查看情节提要或 xib 文件的设备、方向和缩放：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![底部工具栏，用于为设计图面选择设备和方向](introduction-images/13-bottomtoolbar-vsmac.png "底部工具栏，用于为设计图面选择设备和方向")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![底部工具栏，用于为设计图面选择设备和方向](introduction-images/13-bottomtoolbar-vs.png "底部工具栏，用于为设计图面选择设备和方向")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>设备和方向

展开后，底部工具栏将显示适用于当前文档的所有设备、方向和/或适应。 单击它们会更改设计图面上显示的视图。 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![底部工具栏，展开以显示设备和方向](introduction-images/14-bottomtoolbarexpanded-vsmac.png "底部工具栏，展开以显示设备和方向")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![底部工具栏，展开以显示设备和方向](introduction-images/14-bottomtoolbarexpanded-vs.png "底部工具栏，展开以显示设备和方向")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

请注意，选择设备和方向仅会更改 iOS 设计器预览设计的方式。 无论当前选择哪一项，新添加的约束都将应用于所有设备和方向，除非已使用 "**编辑特征**" 按钮进行指定。

[启用](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes)[大小类](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)后，展开的底部工具栏中将显示 "编辑特征" 按钮。  单击 "**编辑特征**" 按钮将显示基于所选设备和方向所表示的大小类创建界面变体的选项。 请开考虑以下示例：

- 如果选择了**IPHONE SE** / **纵向**，则 segue 将提供用于为紧凑型宽度、常规高度大小类创建界面变体的选项。 
- 如果选择了**IPad Pro 9.7 "**  / **横向** / **全屏**显示，则 segue 将提供用于为常规宽度、常规高度大小类创建界面变体的选项。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![底部工具栏用于改变按大小类的接口](introduction-images/15-edittraitsbutton-vsmac.png "底部工具栏用于改变按大小类的接口")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![底部工具栏用于改变按大小类的接口](introduction-images/15-edittraitsbutton-vs.png "底部工具栏用于改变按大小类的接口")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>缩放控件

设计图面支持通过多个控件进行缩放：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![底部工具栏中的缩放控件](introduction-images/16-zoomcontrols-vsmac.png "底部工具栏中的缩放控件")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![底部工具栏中的缩放控件](introduction-images/16-zoomcontrols-vs.png "底部工具栏中的缩放控件")

-----

这些控件包括：

1. 缩放到合适大小
2. 缩小
3. 放大
4. 实际大小（1:1 像素大小）

这些控件在设计图面上调整缩放。 它们不会影响应用程序在运行时的用户界面。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

### <a name="properties-pad"></a>Properties Pad

使用**Properties Pad**编辑控件的标识、视觉样式、可访问性和行为。 以下屏幕截图说明了按钮的**Properties Pad**选项：

[![按钮的 Properties Pad](introduction-images/17-buttonpropertiespad-vsmac.png "按钮的 Properties Pad")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Properties Pad 节

**Properties Pad**包含三个部分：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>属性窗口

使用 "**属性" 窗口**可以编辑控件的标识、视觉样式、可访问性和行为。 以下屏幕截图说明了按钮的 "**属性" 窗口**选项：

[![按钮的 "属性" 窗口](introduction-images/17-buttonpropertieswindow-vs.png "按钮的 "属性" 窗口")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>"属性" 窗口节

"**属性" 窗口**包含三个部分：

-----

1. **小组件**–控件的主要属性，如名称、类、样式属性等。通常在此处放置用于管理控件内容的属性。
2. **布局**–跟踪控件位置和大小（包括限制和框架）的属性列在此处。
3. **事件**–事件和事件处理程序在此处指定。 用于处理触控、点击、拖动等事件。事件也可以直接在代码中处理。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>编辑 Properties Pad 中的属性

除了在设计图面上进行可视编辑之外，iOS 设计器还支持在**Properties Pad**中编辑属性。 可用属性因所选控件而变化，如以下屏幕截图所示：

[![按钮属性](introduction-images/18a-buttonpropertiespad-vsmac.png "按钮属性")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![查看控制器属性](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "查看控制器属性")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>在 "属性" 窗口中编辑属性

除了在设计图面上进行可视编辑之外，iOS 设计器还支持在 "**属性" 窗口**中编辑属性。 可用属性因所选控件而变化，如以下屏幕截图所示：

[![按钮属性](introduction-images/18a-buttonpropertieswindow-vs.png "按钮属性")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![查看控制器属性](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "查看控制器属性")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> 现在，Properties Pad 的 "标识" 部分显示一个**模块**字段。 仅当与 Swift 类互操作时，才需要填写此部分。 使用它为 Swift 类输入模块名称，这些类为带命名空间。

#### <a name="default-values"></a>默认值

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

**Properties Pad**中的许多属性不显示值或默认值。 但是，应用程序的代码可能仍会修改这些值。 **Properties Pad**不显示在代码中设置的值。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

"**属性" 窗口**中的许多属性不显示值或默认值。 但是，应用程序的代码可能仍会修改这些值。 "**属性" 窗口**不会显示在代码中设置的值。

-----

#### <a name="event-handlers"></a>事件处理程序

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要为各种事件指定自定义事件处理程序，请使用**Properties Pad**的**事件**选项卡。 例如，在下面的屏幕截图中，`HandleClick` 方法处理按钮在事件**内的触摸**：

[![Properties Pad，并为按钮设置了事件处理程序](introduction-images/19-buttonpropertiespadevents-vsmac.png "Properties Pad，并为按钮设置了事件处理程序")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要为各种事件指定自定义事件处理程序，请使用 "**属性" 窗口**的 "**事件**" 选项卡。 例如，在下面的屏幕截图中，`HandleClick` 方法处理按钮在事件**内的触摸**：

[!["属性" 窗口，其中为按钮设置了事件处理程序](introduction-images/19-buttonpropertieswindowevents-vs.png ""属性" 窗口，其中为按钮设置了事件处理程序")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

指定事件处理程序后，必须将同名的方法添加到相应的视图控制器类中。 否则，在点击按钮时将发生 `unrecognized selector` 异常：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![无法识别的选择器异常](introduction-images/20-unrecognizedselector-vsmac.png "无法识别的选择器异常")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

请注意，在**Properties Pad**中指定了事件处理程序后，IOS 设计器将立即打开相应的代码文件，并提供插入方法声明。 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![无法识别的选择器异常](introduction-images/20-unrecognizedselector-vs.png "无法识别的选择器异常")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

有关使用自定义事件处理程序的示例，请参阅[Hello，iOS 入门指南](~/ios/get-started/hello-ios/index.md)。

### <a name="outline-view"></a>大纲视图

IOS 设计器还可以以大纲形式显示接口的控件层次结构。 可以通过选择 "**文档大纲**" 选项卡来获取大纲，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![文档大纲](introduction-images/21-buttonoutlineview-vsmac.png "文档大纲")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![文档大纲](introduction-images/21-buttonoutlineview-vs.png "文档大纲")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

大纲视图中的所选控件与设计图面上的选定控件保持同步。  此功能可用于从深度嵌套的接口层次结构中选择项。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="revert-to-xcode"></a>恢复到 Xcode

可以 Interface Builder 交换使用 iOS 设计器和 Xcode。 若要在 Xcode Interface Builder 中打开情节提要或 xib 文件，请右键单击该文件，然后选择 "**使用 > Xcode Interface Builder 打开**"，如下面的屏幕截图所示：

[![在 Xcode 中打开情节提要 Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode 中打开情节提要 Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode Interface Builder 中进行编辑后，请保存文件并返回到 Visual Studio for Mac。 更改将同步到 Xamarin iOS 项目。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>恢复到 Xcode

可以 Interface Builder 交换使用 iOS 设计器和 Xcode，但 Xcode Interface Builder 仅在 Mac 上可用。 若要在 Mac 上的 Xcode Interface Builder 中打开情节提要或 xib 文件，请在[Visual Studio for Mac](/visualstudio/mac/)中打开包含 Xamarin iOS 项目的解决方案，右键单击该文件，然后选择 "**使用 > Xcode Interface Builder 打开**"，如下面的屏幕截图所示：

[![在 Xcode 中打开情节提要 Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode 中打开情节提要 Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode Interface Builder 中进行编辑后，请保存文件并返回到 Visual Studio for Mac。 更改将同步到 Xamarin iOS 项目。

-----

## <a name="xib-support"></a>。 xib 支持

IOS 设计器支持创建、编辑和管理 xib 文件。 这些是 XML 文件，表示可以添加到应用程序的视图层次结构中的单个自定义视图。 Xib 文件通常表示应用程序中单个视图或屏幕的接口，而情节提要表示多个屏幕和它们之间的转换。

有很多关于解决方案的 xib 文件、演示图板或代码的看法，最适用于创建和维护用户界面。 事实上，没有完美的解决方案，并且始终需要考虑到手头的工作的最佳工具。 也就是说，xib 文件通常最适用于在应用中的多个位置表示需要的自定义视图，例如自定义表视图单元。 

以下食谱介绍了有关使用 xib 文件的更多文档：

- [使用 xib 模板](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [使用 xib 创建自定义 TableViewCell](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [使用 xib 创建启动屏幕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

有关使用情节提要的详细信息，请参阅[情节提要简介](~/ios/user-interface/storyboards/index.md)。

此和其他与 iOS 设计器相关的指南介绍了如何使用情节提要作为构建用户界面的标准方法，因为大多数 Xamarin 新项目模板默认提供情节提要。

## <a name="summary"></a>Summary

本指南提供了 iOS 设计器简介，描述了其功能并概述了用于设计精美用户界面的工具。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 设计控制演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello，iOS](~/ios/get-started/hello-ios/index.md)
- [Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Android Designer 概述](~/android/user-interface/android-designer/index.md)
- [分部类和方法](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [深入了解 Xamarin Designer for iOS-演化2014（视频）](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [使用 iOS 设计器创建启动屏幕（视频）](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
