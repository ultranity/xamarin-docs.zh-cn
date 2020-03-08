---
title: Xamarin 中的统一情节提要
description: 本文档介绍了 Xamarin 中的统一情节提要。 统一的情节提要允许开发人员使用单个界面定义支持多个屏幕大小。
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 13891100d3571f9e847243172aa974072f46e7fe
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915174"
---
# <a name="unified-storyboards-in-xamarinios"></a>Xamarin 中的统一情节提要

iOS 8 包含一种用于创建用户界面（统一情节提要）的全新、易用的机制。 使用单个情节提要来涵盖所有不同的硬件屏幕大小，可以在 "设计一次，使用-多" 样式中创建快速、响应性视图。

由于开发人员不再需要为 iPhone 和 iPad 设备创建单独的特定情节提要，因此它们可以灵活地使用通用接口设计应用程序，然后针对不同大小的类自定义该接口。 通过这种方式，可以将应用程序调整为每个外形规格的优点，并调整每个用户界面以提供最佳体验。

<a name="size-classes" />

## <a name="size-classes"></a>大小类

在 iOS 8 之前，开发人员使用 `UIInterfaceOrientation` 和 `UIInterfaceIdiom` 来区分纵向模式和横向模式，以及在 iPhone 和 iPad 设备之间进行区分。 在 iOS8 中，通过使用*大小类*确定方向和设备。

设备是按大小类定义的，在垂直和水平轴中，在 iOS 8 中有两种类型的类：

- **常规**–这适用于大屏幕尺寸（如 iPad）或小尺寸（如 `UIScrollView`）的小工具。
- **Compact** –适用于较小的设备（如 iPhone）。 此大小会考虑设备的方向。

如果将两个概念一起使用，则结果为 2 x 2 网格，该网格定义可在不同方向上使用的不同可能大小，如下图所示：

 [![](unified-storyboards-images/sizeclassgrid.png "A 2 x 2 grid that defines the different possible sizes that can be used in Regular and Compact orientations")](unified-storyboards-images/sizeclassgrid.png#lightbox)

开发人员可以创建一个视图控制器，该控制器使用可能导致不同布局的四种可能性中的任何一种（如上图所示）。

### <a name="ipad-size-classes"></a>iPad 大小类

由于大小的原因，iPad 具有适用于这两个方向的**常规**类大小。

 [![](unified-storyboards-images/image1.png "iPad Size Classes")](unified-storyboards-images/image1.png#lightbox)

### <a name="iphone-size-classes"></a>iPhone 大小类

IPhone 根据设备的方向提供不同大小的类：

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone Size Classes")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

- 当设备处于纵向模式时，屏幕会**水平和垂直**地显示**精简**类
- 当设备处于横向模式时，屏幕类会从纵向模式反转。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 加上大小类

当纵向时，大小与之前的 Iphone 相同，但在横向不同：

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 Plus Size Classes")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

由于 iPhone 6 Plus 具有足够大的屏幕，因此它能够在横向模式下具有常规的宽度大小类。

### <a name="support-for-a-new-screen-scale"></a>支持新的屏幕缩放

IPhone 6 Plus 使用屏幕缩放系数为3.0 （原始 iPhone 屏幕分辨率的三倍）的新 Retina 高清显示器。 若要在这些设备上提供可能的最佳体验，请包括为此屏幕缩放设计的新图稿。 在 Xcode 6 及更高版本中，资产目录可以包含以1x、2x 和3倍速大小的图像;只需添加新的图像资产，在 iPhone 6 Plus 上运行时，iOS 就会选择正确的资产。

IOS 中的映像加载行为还识别了图像文件上的 `@3x` 后缀。 例如，如果开发人员在应用程序捆绑包中包括以下文件名的图像资产（采用不同的分辨率）： `MonkeyIcon.png`、`MonkeyIcon@2x.png`和 `MonkeyIcon@3x.png`。 当开发人员使用以下代码加载图像时，将自动使用 iPhone 6 上的 `MonkeyIcon@3x.png` 映像：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```

或者，如果使用 iOS 设计器将图像分配给 UI 元素，则在 iPhone 6 Plus 上将再次自动使用 `MonkeyIcon@3x.png` `MonkeyIcon.png`。

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>动态启动屏幕

启动屏幕文件在启动 iOS 应用程序时显示为初始屏幕，以便向用户提供对应用实际启动的反馈。 在 iOS 8 之前，开发人员必须为应用程序运行的每种设备类型、方向和屏幕分辨率包含多个 `Default.png` 的图像资产。

对于 iOS 8，开发人员可在 Xcode 中创建单个原子 `.xib` 文件，该文件使用自动布局和大小类来创建可用于每个设备、分辨率和方向的*动态启动屏幕*。 这不仅减少了开发人员创建和维护所有必需的图像资产所需的工作量，而且还减少了应用程序的已安装捆绑的大小。

## <a name="traits"></a>Traits

特性是一些属性，可用于确定在其环境更改时布局的变化方式。 它们由一组属性（根据 `UIUserInterfaceSizeClass``HorizontalSizeClass` 和 `VerticalSizeClass`）以及接口用法（`UIUserInterfaceIdiom`）和显示比例组成。

所有上述状态都在 Apple 引用为特征集合（`UITraitCollection`）的容器中打包，其中不仅包含属性，还包含其值。

## <a name="trait-environment"></a>特征环境

特性环境是 iOS 8 中的新接口，能够返回以下对象的特征集合：

- 屏幕（`UIScreens`）。
- Windows （`UIWindows`）。
- 查看控制器（`UIViewController`）。
- 视图（`UIView`）。
- 表示控制器（`UIPresentationController`）。

开发人员使用特性环境返回的特征集合来确定应如何布置用户界面。

所有特征环境都使层次结构如下图所示：

 [![](unified-storyboards-images/viewhierarchy.png "The Trait Environments hierarchy diagram")](unified-storyboards-images/viewhierarchy.png#lightbox)

默认情况下，上述每个特征环境的特征集合将从父环境流向子环境。

除了获取当前特征集合外，特征环境还具有 `TraitCollectionDidChange` 方法，该方法可在视图或视图控制器子类中被重写。 开发人员可以使用此方法来修改依赖于这些特征发生更改的特征的任何 UI 元素。

## <a name="typical-trait-collections"></a>典型特征集合

本部分介绍用户在使用 iOS 8 时将遇到的典型类型的特征集。

下面是开发人员在 iPhone 上可能看到的典型特征集合：

|properties|值|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|常规|
|`UserInterfaceIdom`|电话|
|`DisplayScale`|2.0|

上述集将表示完全限定的特征集，因为它具有其所有特征属性的值。

还可以具有一个特征集合，其中缺少某些值（Apple 将其称为*未指定*）：

|properties|值|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|“未指定”|
|`UserInterfaceIdom`|“未指定”|
|`DisplayScale`|“未指定”|

不过，通常情况下，当开发人员为特征集合请求特征环境时，它将返回完全限定的集合，如以上示例中所示。

如果特性环境（如视图或视图控制器）不在当前视图层次结构中，开发人员可能会获得一个或多个特征属性的未指定值。

如果开发人员使用 Apple 提供的一种创建方法（如 `UITraitCollection.FromHorizontalSizeClass`）来创建新的集合，则开发人员还将获得部分限定的特征集合。

可对多个特征集合执行的一项操作是将它们彼此进行比较，这涉及到一个特征集合（如果它包含另一个）。 *包含*的含义是，对于第二个集合中指定的任何特征，值必须与第一个集合中的值完全匹配。

若要测试两个特性，请使用 `UITraitCollection` 的 `Contains` 方法，并传入要测试的特性的值。

开发人员可以在代码中手动执行比较，以确定如何布局视图或查看控制器。 不过，`UIKit` 在内部使用此方法来提供它的某些功能（例如，在外观代理中）。

## <a name="appearance-proxy"></a>外观代理

外观代理是在早期版本的 iOS 中引入的，以允许开发人员自定义视图的属性。 它已在 iOS 8 中扩展以支持特征集合。

外观代理现在包含新方法 `AppearanceForTraitCollection`，该方法为已传入的给定特征集合返回新的外观代理。 开发人员对该外观代理执行的任何自定义仅会对符合指定特征集合的视图生效。

通常，开发人员会将部分指定的特征集合传递到 `AppearanceForTraitCollection` 方法，例如刚指定了 Compact 的水平大小的类，以便他们可以在应用程序中自定义横向精简的任何视图。

## <a name="uiimage"></a>UIImage

Apple 向添加了特征集合的另一个类 `UIImage`。 在过去，开发人员必须指定要在应用程序中包括的任何位图图形资产的 @1X 和 @2x 版本（例如图标）。

iOS 8 已扩展为允许开发人员在基于特征集合的映像目录中包含多个版本的映像。 例如，开发人员可以包含一个较小的图像，用于处理精简特征类和任何其他集合的完全大小的图像。

当在 `UIImageView` 类内部使用其中一种图像时，"图像" 视图将自动显示其特征集合的正确版本的图像。 如果特征环境发生变化（例如，用户将设备从纵向切换为横向），则图像视图将自动选择新图像大小以匹配新的特征集合，并更改其大小以匹配当前版本的图像会.

## <a name="uiimageasset"></a>UIImageAsset

Apple 向 iOS 8 添加了一个名为 "`UIImageAsset`" 的新类，使开发人员可以更好地控制图像选择。

图像资产会包装图像的所有不同版本，并允许开发人员询问与已传入的特征集合匹配的特定图像。 可以动态地从图像资产中添加或删除图像。

有关图像资产的详细信息，请参阅 Apple 的[UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)文档。

## <a name="combining-trait-collections"></a>组合特征集合

开发人员可以对特征集合执行的另一个函数是添加两个，它们将导致组合集合，其中一个集合中的未指定值将替换为第二个集合中的指定值。 使用 `UITraitCollection` 类的 `FromTraitsFromCollections` 方法完成此操作。

如上所述，如果其中一个特征集合未指定任何特征并且在另一个特征集合中指定，则该值将设置为指定的版本。 但是，如果指定了多个版本的给定值，则最后一个特性集合中的值将是使用的值。

## <a name="adaptive-view-controllers"></a>自适应视图控制器

本部分将介绍如何在开发人员的应用程序中将 iOS 视图和视图控制器采用了特征和大小类的概念。

### <a name="split-view-controller"></a>拆分视图控制器

IOS 8 中已更改的其中一个视图控制器类是 `UISplitViewController` 类。 过去，开发人员通常会在 iPad 版应用程序上使用拆分视图控制器，然后他们必须为 iPhone 版本的应用提供完全不同的视图层次结构版本。

在 iOS 8 中，`UISplitViewController` 类在平台（iPad 和 iPhone）上可用，这允许开发人员创建一个可同时适用于 iPhone 和 iPad 的视图控制器层次结构。

当 iPhone 处于横向时，拆分视图控制器将并排显示其视图，就像在 iPad 上显示一样。

### <a name="overriding-traits"></a>覆盖特征

特性环境从父容器级联向下级联到子容器，如下图所示，以横向显示 iPad 上的拆分视图控制器：

 [![](unified-storyboards-images/cascadingclasses01.png "A Split View Controller on an iPad in the landscape orientation")](unified-storyboards-images/cascadingclasses01.png#lightbox)

由于 iPad 在水平和垂直方向上都有一个常规大小类，因此 "拆分" 视图将显示主视图和详细信息视图。

在 iPhone 上，在这两个方向上，"拆分" 视图控制器仅显示详细信息视图，如下所示：

 [![](unified-storyboards-images/cascadingclasses02.png "The Split View Controller only displays the detail view")](unified-storyboards-images/cascadingclasses02.png#lightbox)

在开发人员想要以横向方向显示 iPhone 上的主视图和详细信息视图的应用程序中，开发人员必须为拆分视图控制器插入父容器，并覆盖特征集合。 如下图所示：

 [![](unified-storyboards-images/cascadingclasses03.png "The developer must insert a parent container for the Split View Controller and override the Trait Collection")](unified-storyboards-images/cascadingclasses03.png#lightbox)

将 `UIView` 设置为拆分视图控制器的父级，并对在新的特征集合中传递并以拆分视图控制器为目标的视图调用 `SetOverrideTraitCollection` 方法。 新的特征集合将覆盖 `HorizontalSizeClass`，并将其设置为 `Regular`，以便拆分视图控制器将以横向方向显示 iPhone 上的主视图和详细信息视图。

请注意，`VerticalSizeClass` 设置为 `unspecified`，这允许将新的特性集合添加到父项上的 "特性" 集合中，从而为子拆分视图控制器提供 `Compact VerticalSizeClass`。

### <a name="trait-changes"></a>特征更改

本部分将详细介绍特征集如何在特征环境改变时进行转换。 例如，将设备从纵向旋转到横向时。

 [![](unified-storyboards-images/traittransitions01.png "The portrait to landscape Trait Changes overview")](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 执行一些设置，以便准备进行过渡。 接下来，系统会对转换状态进行动画处理。 最后，iOS 8 清除在转换过程中所需的任何临时状态。

iOS 8 提供若干次回调，开发人员可以使用这些回调来参与特征更改，如下表所示：

|阶段|回调|说明|
|--- |--- |--- |
|设置|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>此方法在特性集合设置为其新值之前在特征更改开始时调用。</li><li>当特征集合的值已更改，但在发生任何动画之前，将调用方法。</li></ul>|
|动画|`WillTransitionToTraitCollection`|传递给此方法的转换协调器具有 `AnimateAlongside` 属性，使开发人员能够添加将与默认动画一起执行的动画。|
|清理|`WillTransitionToTraitCollection`|为开发人员提供一种方法，用于在发生过渡后包含自己的清理代码。|

`WillTransitionToTraitCollection` 方法非常适合用于对视图控制器进行动画处理，以及特征集合更改。 `WillTransitionToTraitCollection` 方法仅适用于视图控制器（`UIViewController`），不能用于其他特性环境（如 `UIViews`）。

`TraitCollectionDidChange` 非常适合用于处理 `UIView` 类，开发人员希望在改变特征时更新 UI。

### <a name="collapsing-the-split-view-controllers"></a>折叠拆分视图控制器

现在，让我们详细了解拆分视图控制器从两列到一个列视图的折叠情况。 作为此更改的一部分，需要执行两个过程：

- 默认情况下，展开后，拆分视图控制器将使用主视图控制器作为视图。 开发人员可以重写此行为，方法是重写 `UISplitViewControllerDelegate` 的 `GetPrimaryViewControllerForCollapsingSplitViewController` 方法，并提供他们想要在折叠状态下显示的任何视图控制器。
- 辅助视图控制器必须合并到主视图控制器。 通常，开发人员无需在此步骤中执行任何操作;拆分视图控制器包括基于硬件设备的此阶段的自动处理。 但是，在某些特殊情况下，开发人员需要与此更改进行交互。 如果调用了 `UISplitViewControllerDelegate` 的 `CollapseSecondViewController` 方法，则允许在折叠发生时显示母版视图控制器，而不是详细信息视图。

### <a name="expanding-the-split-view-controller"></a>展开拆分视图控制器

现在，让我们详细了解拆分视图控制器从折叠状态扩展后会发生的情况。 同样，需要执行两个阶段：

- 首先，定义新的主视图控制器。 默认情况下，拆分视图控制器将自动从折叠视图使用主视图控制器。 同样，开发人员可以使用 `UISplitViewControllerDelegate` 的 `GetPrimaryViewControllerForExpandingSplitViewController` 方法来重写此行为。
- 选择主视图控制器后，必须重新创建辅助视图控制器。 同样，拆分视图控制器包括基于硬件设备的此阶段的自动处理。 开发人员可以通过调用 `UISplitViewControllerDelegate` 的 `SeparateSecondaryViewController` 方法来重写此行为。

在拆分视图控制器中，主视图控制器通过实现 `UISplitViewControllerDelegate`的 `CollapseSecondViewController` 和 `SeparateSecondaryViewController` 方法，在展开和折叠视图中扮演着一个部分。 `UINavigationController` 实现这些方法以自动推送和弹出辅助视图控制器。

### <a name="showing-view-controllers"></a>显示视图控制器

Apple 对 iOS 8 进行的另一项更改是开发人员显示视图控制器的方式。 过去，如果应用程序有叶视图控制器（如表视图控制器），并且开发人员显示了不同的控制器（例如，为了响应用户点击某个单元），则该应用程序将通过控制器层次结构返回到导航视图控制器并对其调用 `PushViewController` 方法，以显示新的视图。

这会在导航控制器和它运行时所处的环境之间出现紧密耦合。 在 iOS 8 中，Apple 通过提供两种新方法来分离此内容：

- `ShowViewController` –改编以根据其环境显示新的视图控制器。 例如，在 `UINavigationController` 它只是将新视图推送到堆栈上。 在拆分视图控制器中，新的视图控制器将作为新的主视图控制器显示在左侧。 如果没有容器视图控制器，则新视图将显示为模式视图控制器。
- `ShowDetailViewController` –工作方式类似于 `ShowViewController`，但在拆分视图控制器上实现，用于将详细信息视图替换为传入的新视图控制器。 如果拆分视图控制器是折叠的（在 iPhone 应用程序中可能会看到），则调用将被重定向到 `ShowViewController` 方法，新视图将显示为主视图控制器。 同样，如果不存在容器视图控制器，则新视图将显示为模式视图控制器。

这些方法的工作方式是从叶视图控制器开始，遍历视图层次结构，直到找到正确的容器视图控制器来处理新视图的显示。

开发人员可以在自己的自定义视图控制器中实现 `ShowViewController` 和 `ShowDetailViewController`，以获取 `UINavigationController` 和 `UISplitViewController` 提供的相同自动化功能。

### <a name="how-it-works"></a>工作原理

在本部分中，我们将介绍如何在 iOS 8 中实际实现这些方法。 首先，让我们看一看新的 `GetTargetForAction` 方法：

 [![](unified-storyboards-images/gettargetforaction.png "The new GetTargetForAction method")](unified-storyboards-images/gettargetforaction.png#lightbox)

此方法将遍历层次结构链，直到找到正确的容器视图控制器。 例如：

1. 如果调用 `ShowViewController` 方法，则实现此方法的链中的第一个视图控制器是导航控制器，因此它将用作新视图的父级。
1. 如果改为调用 `ShowDetailViewController` 方法，则拆分视图控制器是第一个要实现它的视图控制器，因此它将用作父级。

`GetTargetForAction` 方法的工作方式是：查找实现给定操作的视图控制器，然后询问视图控制器（如果它要接收该操作）。 由于此方法是公共的，因此开发人员可以创建自己的自定义方法，就像内置 `ShowViewController` 和 `ShowDetailViewController` 方法一样。

## <a name="adaptive-presentation"></a>自适应演示

在 iOS 8 中，Apple 已将 Segue 演示文稿（`UIPopoverPresentationController`）设为自适应。 因此，Segue 演示视图控制器会自动在常规大小类中显示普通的 Segue 视图，但会在水平紧凑的大小类（如 iPhone）上以全屏方式显示它。

为了适应统一情节提要系统中的更改，创建了一个新的控制器对象用于管理所提供的视图控制器，`UIPresentationController`。 此控制器是从显示视图控制器到关闭视图控制器时创建的。 由于它是一个管理类，因此它可以在视图控制器上被视为一个超级类，因为它对影响视图控制器（如方向）的设备更改进行响应，然后将其反馈到显示控制器控件的视图控制器。

当开发人员使用 `PresentViewController` 方法呈现视图控制器时，会将演示过程的管理传递到 `UIKit`中。 UIKit 处理要创建的样式的正确控制器，其中唯一的例外是将视图控制器的样式设置为 `UIModalPresentationCustom`。 此时，应用程序可以提供自己的 PresentationController，而不是使用 `UIKit` 控制器。

### <a name="custom-presentation-styles"></a>自定义演示样式

使用自定义演示样式，开发人员可以选择使用自定义呈现控制器。 此自定义控制器可用于修改 allied 的视图的外观和行为。

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>使用大小类

本文随附的自适应照片 Xamarin 项目提供了在 iOS 8 统一界面应用程序中使用大小类和自适应视图控制器的工作示例。

尽管应用程序完全是通过代码创建的 UI，但与使用 IOS 设计器并创建统一的情节提要不同，这种方法同样适用。 在本文的后面部分，我们将演示如何在 Xamarin 应用程序中结合使用大小类和统一情节提要和 iOS 设计器。

现在，让我们详细了解自适应照片项目如何实现 iOS 8 中的几个大小类功能，以创建自适应应用程序。

### <a name="adapting-to-trait-environment-changes"></a>适应特征环境更改

当在 iPhone 上运行自适应照片应用程序时，当用户将设备从纵向旋转到横向时，拆分视图控制器将显示大纲视图和详细信息视图：

 [![](unified-storyboards-images/rotation.png "The Split View Controller will display both the master and details view as seen here")](unified-storyboards-images/rotation.png#lightbox)

这是通过以下方式实现的：重写视图控制器的 `UpdateConstraintsForTraitCollection` 方法，并基于 `VerticalSizeClass`的值调整约束。 例如：

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>添加过渡动画

自适应照片应用程序中的拆分视图控制器从折叠展开为展开时，会通过覆盖视图控制器的 `WillTransitionToTraitCollection` 方法，将动画添加到默认动画。 例如：

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>覆盖特征环境

如上所示，当 iPhone 设备处于横向视图中时，自适应照片应用程序强制拆分视图控制器显示详细信息和主视图。

这是通过在视图控制器中使用以下代码来实现的：

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>展开和折叠拆分视图控制器

接下来，我们将探讨如何在 Xamarin 中实现拆分视图控制器的展开和折叠行为。 在 `AppDelegate`中，创建拆分视图控制器后，将分配其委托来处理这些更改：

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

`SeparateSecondaryViewController` 方法测试是否正在显示照片，并根据该状态采取措施。 如果未显示任何照片，它会折叠辅助视图控制器，以便显示母版视图控制器。

展开拆分视图控制器以查看堆栈上是否存在任何照片时，将使用 `CollapseSecondViewController` 方法（如果有的话）。

### <a name="moving-between-view-controllers"></a>在视图控制器之间移动

接下来，让我们看看自适应照片应用程序如何在视图控制器之间移动。 当用户从表中选择某个单元格时，在 `AAPLConversationViewController` 类中，将调用 `ShowDetailViewController` 方法来显示详细信息视图：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>显示泄露指示器

在自适应照片应用程序中，有几个位置会根据特征环境的变化隐藏或显示泄露指示器。 此操作通过以下代码进行处理：

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

这些使用以上详细讨论的 `GetTargetViewControllerForAction` 方法实现。

当表视图控制器正在显示数据时，它将使用上述方法来查看是否将发生推送，以及是否相应地显示或隐藏泄露指示器：

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>新建 `ShowDetailTargetDidChangeNotification` 类型

Apple 添加了一个新的通知类型，用于从拆分视图控制器内使用大小类和特征环境，`ShowDetailTargetDidChangeNotification`。 只要拆分视图控制器的目标详细信息视图发生更改（如控制器展开或折叠时），就会发送此通知。

自适应照片应用程序使用此通知在详细信息视图控制器发生更改时更新泄露指示器的状态：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

详细了解自适应照片应用程序，以查看大小类、特征集合和自适应视图控制器可用于在 Xamarin 中轻松创建统一的应用程序的所有方法。

## <a name="unified-storyboards"></a>统一的情节提要

使用 iOS 8 的新手，统一情节提要允许开发人员创建一个统一的情节提要文件，该文件可通过面向多个大小类在 iPhone 和 iPad 设备上显示。 通过使用统一的情节提要，开发人员可编写更少的 UI 特定的代码，并只提供一个接口设计来创建和维护。

统一情节提要的主要优点是：

- 使用适用于 iPhone 和 iPad 的相同情节提要文件。
- 向后部署到 iOS 6 和 iOS 7。
- 在 Xamarin iOS 设计器中预览不同设备、方向和操作系统版本的布局。

此功能在 Visual Studio for Mac 完全受支持

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>启用大小类

默认情况下，任何新的 Xamarin iOS 项目都将使用大小类。 若要在更早项目的情节提要内使用大小类和自适应 Segue，必须先将其从 iOS 设计器内部转换为 Xcode 6 统一情节提要格式。

为此，请在 iOS 设计器中打开要转换的情节提要，并选中 "**使用大小类**" 复选框：

 [![](unified-storyboards-images/sizeclass01.png "The Use Size Classes check box")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 设计器将确认开发人员想要将情节提要的格式转换为使用大小类：

 [![](unified-storyboards-images/sizeclass02.png "The use Size Classes alert")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 还必须检查自动布局的大小类才能正常工作。

### <a name="generic-device-types"></a>泛型设备类型

将情节提要转换为使用大小类后，它将在 Design Surface 中重新显示，并且作为设备的**视图**将为泛型：

 [![](unified-storyboards-images/sizeclass03.png "View as a Generic device type")](unified-storyboards-images/sizeclass03.png#lightbox)

选择 "通用设备类型" 后，所有视图控制器都将调整为 600 x 600 正方形。 此正方形表示任何宽度和高度的大小。 当 iOS 设计器处于此模式下时，任何编辑都将应用于所有大小类。

开发人员还可以选择查看作为 iPhone 的设计图面：

 [![](unified-storyboards-images/sizeclass04.png "Viewing the design surface as an iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

或者将其查看为 iPad：

 [![](unified-storyboards-images/sizeclass05.png "Viewing the design surface as an iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>选择大小类

Size 类选择器按钮位于 Design Surface 的左上角（位于视图附近）。 它允许开发人员选择当前正在编辑的大小类：

 [![](unified-storyboards-images/sizeclass06.png "Select a Size Class")](unified-storyboards-images/sizeclass06.png#lightbox)

选择器将显示大小类选择作为 3 x 3 网格。 网格中的每个方形都表示 Width 类和 Height 类的组合。 中心方块选择任意宽度/任意高度大小类（统一情节提要的默认视图）。 选中此方形后，开发人员将编辑默认布局，此布局由所有其他配置继承。

网格的左上角的方块表示紧凑型宽度/压缩高度大小类：

 [![](unified-storyboards-images/sizeclass07.png "The Compact Width/Compact Height Size Class")](unified-storyboards-images/sizeclass07.png#lightbox)

此模式对应于处于横向方向的 iPhone。 网格右下角的方形表示常规宽度/常规高度大小类，表示 iPad：

 [![](unified-storyboards-images/sizeclass08.png "The Regular Width/Regular Height Size Class")](unified-storyboards-images/sizeclass08.png#lightbox)

若要为 iPhone 编辑纵向布局，请选择左下角的方块。 这表示紧凑型宽度/常规高度大小类：

 [![](unified-storyboards-images/sizeclass09.png "The Compact Width/Regular Height Size Class")](unified-storyboards-images/sizeclass09.png#lightbox)

在正方形中单击以将其选中，Design Surface 会更改视图控制器的大小以匹配新的所选内容：

 [![](unified-storyboards-images/sizeclass10.png "The Design Surface will change the size of the View Controllers to match the new selection as shown")](unified-storyboards-images/sizeclass10.png#lightbox)

请参阅本文的 Size Class 部分，了解有关大小类及其如何影响 Iphone 和 Ipad 布局的详细信息。

### <a name="adaptive-segue-types"></a>自适应 Segue 类型

如果开发人员以前使用过情节提要，则他们将熟悉**现有的 segue、** **模式**和**segue**类型。 在统一情节提要文件上启用大小类时，将提供以下自适应 Segue 类型（与上述新视图控制器 API 对应的类型）：**显示**和**显示详细信息**。

> [!IMPORTANT]
> 启用大小类后，任何现有的 segue 都将转换为新类型。

采用一个使用统一情节提要的 iOS 8 应用程序示例，该应用程序在母版视图中具有一个简单的游戏导航菜单。 如果用户单击菜单按钮，则在 iPad 上运行时，所选项目的视图控制器应显示在拆分视图控制器的 "详细信息" 部分中。 在 iPhone 上，该项的视图控制器应推送到导航堆栈上。

若要实现此效果，请在 iOS 设计器控件中单击按钮，并将线条拖到要显示的视图控制器。 释放鼠标按钮后，从 Segue 类型弹出菜单中选择 `Show Detail`：

 [![](unified-storyboards-images/segue01.png "Select Show Detail from the Segue Type Popup menu")](unified-storyboards-images/segue01.png#lightbox)

新的 segue 将在按钮和视图控制器之间创建。 现在，在 iPhone 模拟器中运行该应用程序，将显示主菜单：

 [![](unified-storyboards-images/segue02.png "The Main Menu")](unified-storyboards-images/segue02.png#lightbox)

单击 "**选择游戏**" 按钮，项的视图控制器将被推送到导航堆栈：

 [![](unified-storyboards-images/segue03.png "The items View Controller will be pushed onto the Navigation Stack as shown")](unified-storyboards-images/segue03.png#lightbox)

停止 iPhone 模拟器并在 iPad 模拟器中运行该应用程序。 切换到横向方向，并再次显示主菜单：

 [![](unified-storyboards-images/segue04.png "The main menu displayed")](unified-storyboards-images/segue04.png#lightbox)

同样，单击 "**选择游戏**" 按钮，该项目的 "视图控制器" 将显示在拆分视图控制器的 "详细信息" 部分中：

 [![](unified-storyboards-images/segue05.png "The items View Controller shown in the Details section of the Split View Controller")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>从大小类排除元素

有时，特定大小类中不需要给定元素（如视图、控件或约束）。 若要从大小类中排除元素，请选择要在**Design Surface**中排除的所需项。 滚动到**属性资源管理器**的底部，然后单击 "**齿轮**" 下拉菜单。 选择要从中排除项的**宽度**和**高度**的组合：

[![](unified-storyboards-images/exclude-a.png "Select the combination of Width and Height")](unified-storyboards-images/exclude-a.png#lightbox)

新的*排除事例*将添加到**属性资源管理器**底部的元素中。 接下来，取消选中给定大小类的**已安装**复选框：

[![](unified-storyboards-images/exclude-b.png "Uncheck the Installed checkbox")](unified-storyboards-images/exclude-b.png#lightbox)

将 Design Surface 切换为从中排除项的宽度和高度，并从给定的大小类（而不是整个 UI 设计）中删除它：

 [![](unified-storyboards-images/exclude02.png "Switch the Design Surface to the Width and Height that the item was excluded from")](unified-storyboards-images/exclude02.png#lightbox)

切换回任意宽度/任意高度大小类，并且该元素仍就地存在：

 [![](unified-storyboards-images/exclude03.png "Switching back to the Any Width/Any Height size class")](unified-storyboards-images/exclude03.png#lightbox)

当应用程序在 iPad 模拟器中运行时，将显示元素：

 [![](unified-storyboards-images/exclude04.png "The element shown when the running app in the iPad Simulator")](unified-storyboards-images/exclude04.png#lightbox)

当应用程序在 iPhone 模拟器上运行时，缺少元素：

 [![](unified-storyboards-images/exclude05.png "The element missing when the running app in the iPhone Simulator")](unified-storyboards-images/exclude05.png#lightbox)

若要从元素中删除排除事例，只需在 " **Design Surface**中选择元素，滚动到"**属性资源管理器**"底部，然后单击要删除的事例旁边的" **-** "按钮。

若要查看统一情节提要的实现，请查看附加到本文档 `UnifiedStoryboard` 示例 Xamarin iOS 8 应用程序。

## <a name="dynamic-launch-screens"></a>动态启动屏幕

启动屏幕文件在启动 iOS 应用程序时显示为初始屏幕，以便向用户提供对应用实际启动的反馈。 在 iOS 8 之前，开发人员必须为应用程序运行的每种设备类型、方向和屏幕分辨率包含多个 `Default.png` 的图像资产。 例如，`Default@2x.png`、`Default-Landscape@2x~ipad.png`、`Default-Portrait@2x~ipad.png`等。

在新的 iPhone 6 和 iPhone 6 Plus 设备（以及即将发布的 Apple Watch）中，使用所有现有的 iPhone 和 iPad 设备进行因式分解，这表示必须创建和维护 `Default.png` 启动屏幕图像资产的各种大小、方向和分辨率。 此外，这些文件可能会很大，并且会 "膨胀" 可交付结果应用程序捆绑，增加从 iTunes 应用商店下载应用程序所需的时间（可能是因为它无法通过移动电话网络传递）并增加最终用户设备上所需的存储量。

对于 iOS 8，开发人员可在 Xcode 中创建单个原子 `.xib` 文件，该文件使用自动布局和大小类来创建可用于每个设备、分辨率和方向的*动态启动屏幕*。 这不仅减少了开发人员创建和维护所有必需的图像资产所需的工作量，而且大大减少了应用程序的已安装捆绑的大小。

动态启动屏幕具有以下限制和注意事项：

- 仅使用 `UIKit` 类。
- 使用 `UIView` 或 `UIViewController` 对象的单个根视图。
- 不要对应用程序的代码进行任何连接（不要添加**操作**或**插座**）。
- 不要添加 `UIWebView` 对象。
- 不要使用任何自定义类。
- 不要使用运行时属性。

考虑到上述准则，我们来看看如何向现有 Xamarin iOS 8 项目添加动态启动屏幕。

请执行以下操作：

1. 打开**Visual Studio for Mac**并加载**解决方案**，以向添加动态启动屏幕。
2. 在**解决方案资源管理器**中，右键单击 `MainStoryboard.storyboard` 文件，然后选择 "**打开方式** > **Xcode Interface Builder**：

    [![](unified-storyboards-images/dls01.png "Open With Xcode Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. 在 Xcode 中，选择 "**文件** > **新建** > **文件 ...** "：

    [![](unified-storyboards-images/dls02.png "Select File / New")](unified-storyboards-images/dls02.png#lightbox)
4. 选择 " **iOS** > **用户界面**" > **启动屏幕**，然后单击 "**下一步**" 按钮：

    [![](unified-storyboards-images/dls03.png "Select iOS / User Interface / Launch Screen")](unified-storyboards-images/dls03.png#lightbox)
5. 将该文件命名 `LaunchScreen.xib` 然后单击 "**创建**" 按钮：

    [![](unified-storyboards-images/dls04.png "Name the file LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. 通过添加图形元素并使用布局约束来为给定设备、方向和屏幕大小定位它们，编辑启动屏幕的设计：

    [![](unified-storyboards-images/dls05.png "Editing the design of the launch screen")](unified-storyboards-images/dls05.png#lightbox)
7. 保存对 `LaunchScreen.xib` 的更改。
8. 选择**应用程序目标**和 "**常规**" 选项卡：

    [![](unified-storyboards-images/dls06.png "Select the Applications Target and the General tab")](unified-storyboards-images/dls06.png#lightbox)
9. 单击 "**选择 info.plist** " 按钮，选择 Xamarin 应用的 `Info.plist`，然后单击 "**选择**" 按钮：

    [![](unified-storyboards-images/dls07.png "Select the Info.plist for the Xamarin app")](unified-storyboards-images/dls07.png#lightbox)
10. 在 "**应用程序图标和启动图像**" 部分中，打开 "**启动屏幕文件**" 下拉列表，并选择上面创建的 `LaunchScreen.xib`：

    [![](unified-storyboards-images/dls08.png "Choose the LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. 保存对文件所做的更改，并返回 Visual Studio for Mac。
12. 等待 Visual Studio for Mac 完成与 Xcode 同步更改。
13. 在**解决方案资源管理器**中，右键单击**资源**文件夹，然后选择 "**添加** > **添加文件 ...** "：

    [![](unified-storyboards-images/dls09.png "Select Add / Add Files...")](unified-storyboards-images/dls09.png#lightbox)
14. 选择上面创建的 `LaunchScreen.xib` 文件，并单击 "**打开**" 按钮：

    [![](unified-storyboards-images/dls10.png "Select the LaunchScreen.xib file")](unified-storyboards-images/dls10.png#lightbox)
15. 构建应用程序。

### <a name="testing-the-dynamic-launch-screen"></a>测试动态启动屏幕

在 Visual Studio for Mac 中，选择 iPhone 4 Retina 模拟器并运行该应用程序。 动态启动屏幕将以正确的格式和方向显示：

[![](unified-storyboards-images/dls11.png "The Dynamic Launch Screen displayed in the vertical orientation")](unified-storyboards-images/dls11.png#lightbox)

在 Visual Studio for Mac 中停止应用程序，并选择 iPad iOS 8 设备。 运行应用程序，并为此设备和方向正确设置启动屏幕的格式：

[![](unified-storyboards-images/dls12.png "The Dynamic Launch Screen displayed in the horizontal orientation")](unified-storyboards-images/dls12.png#lightbox)

返回 Visual Studio for Mac，并使应用程序停止运行。

### <a name="working-with-ios-7"></a>使用 iOS 7

若要保持与 iOS 7 的向后兼容性，只需在 iOS 8 应用程序中包括普通 `Default.png` 映像资产。 iOS 将恢复到以前的行为，并在 iOS 7 设备上运行时将这些文件用作启动屏幕。

若要在 Xamarin 中查看动态启动屏幕的实现，请查看附加到此文档的[动态启动](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)屏幕示例 iOS 8 应用程序。

## <a name="summary"></a>总结

本文快速查看大小类及其在 iPhone 和 iPad 设备中的影响布局的方式。 它讨论了特征、特征环境和特征集如何与大小类结合使用，以创建统一的界面。 简单了解自适应视图控制器，以及它们如何使用统一接口内的大小类。 其中介绍了如何从C# Xamarin iOS 8 应用程序内的代码完全实现大小类和统一接口。

最后，本文介绍了使用 Xamarin iOS 设计器创建统一情节提要的基础知识，该设计器可跨 iOS 设备工作，并创建单个动态启动屏幕，每个 iOS 8 设备上都将显示为启动屏幕。

## <a name="related-links"></a>相关链接

- [自适应照片（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [动态启动屏幕（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [IOS8 中的动态布局-演化2014（视频）](https://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
