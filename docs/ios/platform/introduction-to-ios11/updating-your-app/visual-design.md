---
title: IOS 11 中的视觉对象设计更新
description: 本文档介绍 iOS 11 中引入的可视化设计更新。 它讨论了对导航栏、搜索控制器、边距、全屏内容和表视图的更改。
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: e5a61af4cd8a09df3ffddb74658f646aa8edfa1f
ms.sourcegitcommit: ce4670de51e24116a944c778ee64585bd0aae0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088969"
---
# <a name="visual-design-updates-in-ios-11"></a>IOS 11 中的视觉对象设计更新

在 iOS 11 中，Apple 引入了新的视觉更改，包括对导航栏、搜索栏和表视图的更新。 此外，还改进了以使边距和全屏内容更具灵活性。 本指南中介绍了这些更改。 

有关专为 iPhone X 设计的信息，请观看 Apple[设计 Iphone x](https://developer.apple.com/videos/play/fall2017/801/)视频。

## <a name="uikit"></a>UIKit

UIKit 栏已在 iOS 11 中改编，以使其更易于最终用户访问。

其中一项更改是在用户长按某个条形项时显示的新的 HUD 显示。 若要启用此操作，请在 `UIBarItem` 上设置 `largeContentSizeImage` 属性，并通过[资产目录](~/ios/app-fundamentals/images-icons/displaying-an-image.md)添加一个较大的图像：

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>导航栏
iOS 11 引入了新功能，使导航栏标题更易于阅读。 应用可通过将 `PrefersLargeTitles` 属性分配给 true 来显示此更大的标题：

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

在应用中设置更大的标题会使应用上的_所有_导航栏标题显示得更大，如以下屏幕截图中所示：

![大型导航标题](visual-design-images/image7.png)

若要控制在导航栏中显示大标题的时间，请将导航项上的 `LargeTitleDisplayMode` 设置为 `Always`、`Never`或 `Automatic`。

### <a name="search-controller"></a>搜索控制器

iOS 11 使你可以更轻松地将搜索控制器直接添加到导航栏。 创建搜索控制器后，将其添加到导航栏中的 "`SearchController`" 属性：

```csharp
NavigationItem.SearchController = searchController;
```

[通过搜索栏 ![大型导航标题](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

根据你的应用程序的功能，你可能需要也可能不希望在用户滚动列表时隐藏搜索栏。 您可以使用 `HidesSearchBarWhenScrolling` 属性调整此属性。

## <a name="margins"></a>边距

Apple 创建了一个新的属性-`directionalLayoutMargins`-可用于设置视图与子视图之间的空间。 将 `directionalLayoutMargins` 与 `leading` 或 `trailing` 嵌入一起使用。 无论系统是从左到右还是从右到左的语言，应用中的间距都是由 iOS 适当设置的。

在 iOS 10 和之前的版本中，所有视图都具有与之对齐的最小边距大小。 iOS 11 引入了用 `ViewRespectsSystemMinimumLayoutMargins`替代该选项的选项。 例如，如果将此属性设置为 false，则可以将边缘嵌入调整为零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![在 ios 11 中显示边距为零的图像](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>全屏内容

iOS 7[引入](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen)了 `topLayoutGuide`，`bottomLayoutGuide` 作为对视图进行约束的方法，使其不会被 UIKit 条隐藏并且位于屏幕的可视区域中。 它们已在 iOS 11 中弃用，以支持_安全区域_。

安全区域是一种新的方法，用于考虑应用程序的可见空间，以及如何在视图和超级视图之间添加约束。 例如，请看下图：

[![安全区域与顶部和底部布局指南](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

以前，如果你已添加了一个视图，并希望它在上述绿色区域中可见，则会将其限制为 `TopLayoutGuide` 的_底部_和 `BottomLayoutGuide`_顶部_。 在 iOS 11 中，你应将其限制在安全区域的_顶部_和_底部_。 下面是一个示例：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>表视图

IOS 11 中的 UITableView 有许多小但又显著的更改。

默认情况下，页眉、页脚和单元格现在会根据其内容自动调整大小。 若要选择退出此自动调整大小行为，请将 `EstimatedRowHeight`、`EstimatedSectionHeaderHeight`或 `EstimatedSectionFooterHeight` 设置为零。

但是，在某些情况下（例如在 iOS 设计器中添加 UITableViewController 或在 Interface Builder 中使用现有的 Storboards 时），可能需要手动启用自动调整单元格。 为此，请确保在表视图中分别为单元格、页眉和页脚设置以下属性：

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 扩展了行操作的功能。 引入 `UISwipeActionsConfiguration` 来定义一组操作，这些操作应在用户在表视图中的行方向上 swipes 时进行。 此行为与本机 Mail 应用程序的行为类似。 有关详细信息，请参阅[行操作](~/ios/user-interface/controls/tables/row-action.md)指南。

表视图支持 iOS 11 中的拖放。 有关详细信息，请参阅[拖放](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview)指南。

## <a name="related-links"></a>相关链接

- [IOS 11 （Apple）中的新增功能](https://developer.apple.com/ios/)
- [更新了应用商店产品页（Apple）](https://developer.apple.com/app-store/product-page/)
- [设计 iPhone X （Apple）（视频）](https://developer.apple.com/videos/play/fall2017/801/)
- [更新 iOS 11 应用（WWDC）（视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
