---
title: 在 Xamarin 中使用情节提要
description: 本文档介绍如何在 Xamarin 中使用情节提要，检查如何从代码中加载它们，查看控制器生命周期，响应方链，segue，窗口控制器，手势识别器等。
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 06774d13cf053b661dd0b2d24b7df0c0b767b4db
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78919141"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>在 Xamarin 中使用情节提要

情节提要将给定应用的所有 UI 定义为细分为其视图控制器的功能概述。 在 Xcode 的 Interface Builder 中，其中每个控制器都位于其自身的场景中。

[![Xcode 中的情节提要 Interface Builder](indepth-images/intro01.png)](indepth-images/intro01.png#lightbox)

情节提要是一个资源文件（扩展名为 `.storyboard`），在编译和交付 Xamarin 应用程序的捆绑包时包含在其中。 若要为应用定义起始情节提要，请编辑它的 `Info.plist` 文件，然后从下拉框中选择**主界面**： 

[![info.plist 编辑器](indepth-images/sb01.png)](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>从代码加载

有时，你可能需要从代码加载特定情节提要并手动创建视图控制器。 你可以使用以下代码来执行此操作：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName` 加载包含在应用包中的具有给定名称的情节提要文件。 `InstantiateControllerWithIdentifier` 创建具有给定标识的视图控制器的实例。 设计 UI 时，请在 Xcode 的 Interface Builder 中设置标识：

[设置情节提要 ID ![](indepth-images/sb02.png)](indepth-images/sb02.png#lightbox)

或者，您可以使用 `InstantiateInitialController` 方法加载已在 Interface Builder 中分配了初始控制器的视图控制器：

[![设置初始控制器](indepth-images/sb03.png)](indepth-images/sb03.png#lightbox)

它由**情节提要入口点**和上面的 "打开结束" 箭头标记。

<a name="View-Controllers" />

## <a name="view-controllers"></a>查看控制器

视图控制器定义 Mac 应用内的给定信息视图与提供该信息的数据模型之间的关系。 情节提要中的每个顶层场景都代表 Xamarin 应用程序代码中的一个视图控制器。

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>视图控制器生命周期

向 `NSViewController` 类添加了几个新方法，以支持 macOS 中的情节提要。 最重要的方法是使用以下方法来响应由给定视图控制器控制的视图的生命周期：

- `ViewDidLoad`-从情节提要文件加载视图时调用此方法。
- `ViewWillAppear`-在屏幕上显示视图之前，调用此方法。
- `ViewDidAppear`-在屏幕上显示视图后直接调用此方法。
- `ViewWillDisappear`-在从屏幕中删除视图之前，将调用此方法。
- `ViewDidDisappear`-从屏幕中删除视图后直接调用此方法。
- `UpdateViewConstraints`-需要更新定义视图自动布局位置和大小的约束时，将调用此方法。
- `ViewWillLayout`-此方法在屏幕上的布局子视图之前被调用。
- `ViewDidLayout`-在屏幕上布局视图的子视图后直接调用此方法。

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>响应方链

此外，`NSViewControllers` 现在是窗口的_响应方链_的一部分：

[![响应方链](indepth-images/vc01.png)](indepth-images/vc01.png#lightbox)

因此，它们可以通过有线方式接收和响应事件，例如剪切、复制和粘贴菜单项选择。 此自动视图控制器仅在 macOS Sierra （10.12）及更高版本上运行的应用上进行。

<a name="Containment" />

### <a name="containment"></a>Containment

在情节提要中，视图控制器（如拆分视图控制器和选项卡视图控制器）现在可以实现_包含_，使其可以 "包含" 其他子视图控制器：

[![查看控制器包含的示例](indepth-images/vc02.png)](indepth-images/vc02.png#lightbox)

子视图控制器包含一些方法和属性，这些方法和属性可将这些方法和属性关联回其父视图控制器，以及在屏幕上显示和删除视图的方法和属性。

内置于 macOS 中的所有容器视图控制器都具有特定的布局，在创建自己的自定义容器视图控制器时，Apple 建议你遵循此布局：

[![视图控制器布局](indepth-images/vc03.png)](indepth-images/vc03.png#lightbox)

集合视图控制器包含集合视图项的数组，其中每个项都包含一个或多个包含它们自己的视图控制器。

<a name="Segues" />

## <a name="segues"></a>Segue

Segue 提供定义应用 UI 的所有场景之间的关系。 如果你熟悉 iOS 中的情节提要，你会知道 Segue for iOS 通常定义全屏视图之间的转换。 这不同于 macOS，Segue 通常定义 "[包含](#Containment)"，其中一个场景是父场景的子场景。

在 macOS 中，大多数应用都倾向于使用 UI 元素（如拆分视图和选项卡）在同一个窗口中将其视图组合在一起。 不同于 iOS，其中的视图需要在屏幕上转换，因为物理显示空间有限。

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>演示 Segue

对于 macOS 的倾向，在某些情况下，使用_演示 segue_ ，如模式窗口、工作表视图和 Popovers。 macOS 提供下列内置 segue 类型：

- **Show** -显示 Segue 的目标作为非模式窗口。 例如，使用此类型的 Segue 在应用程序中显示文档窗口的另一个实例。
- **Modal** -显示 Segue 的目标作为模式窗口。 例如，使用此类型的 Segue 可显示应用的首选项窗口。
- **工作表**-以附加到父窗口的工作表形式显示 Segue 的目标。 例如，使用此类型的 segue 显示 "查找和替换" 工作表。
- **Segue** -在 segue 窗口中显示 Segue 的目标。 例如，当用户单击 UI 元素时，可以使用此 Segue 类型来提供选项。
- **自定义**-使用开发人员定义的自定义 Segue 类型显示 Segue 的目标。 有关更多详细信息，请参阅下面的 "[创建自定义 segue](#Creating-Custom-Segues) " 部分。

使用 Presentation Segue 时，可以重写父视图控制器的 `PrepareForSegue` 方法以进行演示，以便初始化和变量并向所提供的视图控制器提供任何数据。

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>触发的 Segue

触发的 Segue 允许你指定命名 Segue （通过其在 Interface Builder 中的**标识符**属性），并使它们由事件（例如用户单击按钮或通过在代码中调用 `PerformSegue` 方法）触发：

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

当你对应用程序的 UI 进行布局时，将在 Xcode 的 Interface Builder 中定义 Segue ID：

[输入 Segue 名称 ![](indepth-images/sg02.png)](indepth-images/sg02.png#lightbox)

在充当 Segue 源的视图控制器中，应覆盖 `PrepareForSegue` 方法，并在执行 Segue 之前执行任何所需的初始化，并显示指定的视图控制器：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

或者，你可以重写 `ShouldPerformSegue` 方法，并控制是否实际上通过C#代码执行了 Segue。 对于手动呈现的视图控制器，请调用其 `DismissController` 方法，以便在不再需要时将其从显示中删除。

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>创建自定义 Segue

有时，你的应用程序需要在 macOS 中定义的内置 Segue 未提供 Segue 类型。 如果是这种情况，则可以创建自定义 Segue，以便在对应用程序的 UI 进行布局时，可以在 Xcode 的 Interface Builder 中分配该自定义。

例如，若要创建一个新的 Segue 类型，用来替换窗口中的当前视图控制器（而不是在新窗口中打开目标场景），可以使用以下代码：

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

此处需要注意几个事项：

- 我们正在使用 `Register` 特性向目标-C/macOS 公开此类。
- 我们正在覆盖 `Perform` 方法，以实际执行自定义 Segue 的操作。
- 我们正在将窗口的 `ContentViewController` 控制器替换为 Segue 的目标（目标）定义的控制器。
- 我们将使用 `RemoveFromParentViewController` 方法删除原始视图控制器以释放内存。

若要在 Xcode 的 Interface Builder 中使用此新的 Segue 类型，需要先编译应用，然后切换到 Xcode 并在两个场景之间添加新的 Segue。 将**样式**设置为**Custom** ，并将**Segue 类**设置为 `ReplaceViewSegue` （我们的自定义 Segue 类的名称）：

[![设置 Segue 类](indepth-images/sg01.png)](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>窗口控制器

窗口控制器包含并控制 macOS 应用程序可以创建的不同窗口类型。 对于情节提要，它们具有以下功能：

1. 它们必须提供内容视图控制器。 这与子窗口具有相同的内容视图控制器。
2. `Storyboard` 属性将包含从其加载窗口控制器的情节提要，否则，如果未从情节提要加载，则 `null`。
3. 您可以调用 `DismissController` 方法来关闭给定的窗口并将其从视图中删除。

与视图控制器一样，窗口控制器实现 `PerformSegue`、`PrepareForSegue` 和 `ShouldPerformSegue` 方法，并可用作 Segue 操作的源。

窗口控制器负责 macOS 应用的以下功能：

- 它们管理特定的窗口。
- 它们管理窗口的标题栏和工具栏（如果可用）。
- 它们管理内容视图控制器以显示窗口的内容。

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>手势识别器

MacOS 的笔势识别器与 iOS 中的对应项几乎完全相同，并使开发人员能够轻松地将笔势（如单击鼠标按钮）添加到应用 UI 中的元素。

但是，iOS 中的手势由应用的设计确定（例如，用两根手指点击屏幕），macOS 中的大多数手势都由硬件确定。

通过使用笔势识别器，可以极大地减少向 UI 中的项添加自定义交互所需的代码量。 由于它们可以自动确定双击两次，单击并拖动事件等。

当使用情节提要时，应使用手势识别器来处理用户输入事件，而不是重写视图控制器中的 `MouseDown` 事件。

MacOS 中提供了以下手势识别器：

- `NSClickGestureRecognizer`-向下或向下注册鼠标事件。
- `NSPanGestureRecognizer`-向下注册鼠标按钮、拖动和释放事件。
- `NSPressGestureRecognizer`-寄存器在给定的时间事件内按下鼠标按钮。
- `NSMagnificationGestureRecognizer`-从触控板硬件注册缩放事件。
- `NSRotationGestureRecognizer` 从触控板硬件注册轮换事件。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用情节提要引用

使用情节提要引用，您可以进行大量复杂的情节提要设计，并将其分解成更小的情节提要，使其从原始情节提要中引用，从而消除复杂性并使生成的各个演示图板更易于设计和维护。

此外，情节提要引用可以提供指向同一情节提要中的另一个场景或不同场景中的特定场景的_定位点_。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>引用外部情节提要

若要添加对外部情节提要的引用，请执行以下操作：

1. 在**解决方案资源管理器**中，右键单击项目名称，然后选择 "**添加** > **新文件 ...**  > **Mac** > **情节提要**。 输入新情节提要的**名称**，然后单击 "**新建**" 按钮： 

    [添加新情节提要 ![](indepth-images/ref01.png)](indepth-images/ref01.png#lightbox)
2. 在**解决方案资源管理器**中，双击新的情节提要名称以将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。
3. 按通常的方式设计新情节提要的幕后布局，并保存所做的更改： 

    [![设计接口](indepth-images/ref02.png)](indepth-images/ref02.png#lightbox)
4. 切换到要在 Interface Builder 中添加对的引用的情节提要。
5. 将**情节提要引用**从**对象库**拖动到 Design Surface： 

    [选择库中的情节提要引用 ![](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
6. 在 "**属性检查器**" 中，选择上面创建的**情节提要**的名称： 

    [配置引用 ![](indepth-images/ref04.png)](indepth-images/ref04.png#lightbox)
7. 在现有场景上，单击鼠标右键单击 UI 小组件（例如按钮），并创建一个新的 Segue 到刚刚创建的**情节提要引用**。  从弹出菜单中，选择 "**显示**" 以完成 Segue： 

    [![设置 Segue 类型](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
8. 保存对情节提要所做的更改。
9. 返回到 Visual Studio for Mac 以同步你的更改。

当应用程序运行，并且用户单击你从创建 Segue 的 UI 元素时，将显示情节提要引用中指定的外部情节提要的初始窗口控制器。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>引用外部情节提要中的特定场景

若要将引用添加到外部情节提要（而非初始窗口控制器）的特定场景，请执行以下操作：

1. 在**解决方案资源管理器**中，双击外部情节提要将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。
2. 添加新场景并按常规方式设计其布局： 

    [在 Xcode 中设计布局 ![](indepth-images/ref07.png)](indepth-images/ref07.png#lightbox)
3. 在 "**标识检查器**" 中，输入新场景的窗口控制器的**情节提要 ID** ： 

    [设置情节提要 ID ![](indepth-images/ref08.png)](indepth-images/ref08.png#lightbox)
4. 打开要在 Interface Builder 中添加对的引用的情节提要。
5. 将**情节提要引用**从**对象库**拖动到 Design Surface： 

    [从库中选择情节提要引用 ![](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
6. 在 "**标识检查器**" 中，选择你在上面创建的场景的**情节提要**名称和**引用 id** （情节提要 id）： 

    [设置引用 ID ![](indepth-images/ref09.png)](indepth-images/ref09.png#lightbox)
7. 在现有场景上，单击鼠标右键单击 UI 小组件（例如按钮），并创建一个新的 Segue 到刚刚创建的**情节提要引用**。 从弹出菜单中，选择 "**显示**" 以完成 Segue： 

    [![设置 Segue 类型](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
8. 保存对情节提要所做的更改。
9. 返回到 Visual Studio for Mac 以同步你的更改。

当应用程序运行并且用户单击你从其创建 Segue 的 UI 元素时，将显示具有情节提要引用中指定的外部情节提要的给定**情节提要 ID**的场景。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>引用同一情节提要中的特定场景

若要将引用添加到相同情节提要的特定场景，请执行以下操作：

1. 在**解决方案资源管理器**中，双击情节提要将其打开以进行编辑。
2. 添加新场景并按常规方式设计其布局： 

    [![在 Xcode 中编辑情节提要](indepth-images/ref11.png)](indepth-images/ref11.png#lightbox)
3. 在 "**标识检查器**" 中，输入新场景的窗口控制器的**情节提要 ID** ： 

    [设置情节提要 ID ![](indepth-images/ref12.png)](indepth-images/ref12.png#lightbox)
4. 将**情节提要引用**从**工具箱**拖动到 Design Surface： 

    [从库中选择情节提要引用 ![](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
5. 在 "**属性检查器**" 中，选择上面创建的场景的**引用 id** （情节提要 id）： 

    [设置引用 ID ![](indepth-images/ref13.png)](indepth-images/ref13.png#lightbox)
6. 在现有场景上，单击鼠标右键单击 UI 小组件（例如按钮），并创建一个新的 Segue 到刚刚创建的**情节提要引用**。 从弹出菜单中，选择 "**显示**" 以完成 Segue： 

    [选择 Segue 类型 ![](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
7. 保存对情节提要所做的更改。
8. 返回到 Visual Studio for Mac 以同步你的更改。

当应用程序运行，并且用户单击你从其创建 Segue 的 UI 元素时，将显示在情节提要引用中指定的同一情节提要中具有给定**情节提要 ID**的场景。

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>复杂情节提要示例

有关在 Xamarin 应用程序中使用情节提要的复杂示例，请参阅[SourceWriter 示例应用](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

## <a name="related-links"></a>相关链接

- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
