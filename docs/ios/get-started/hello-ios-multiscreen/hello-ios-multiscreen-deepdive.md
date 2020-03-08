---
title: Hello，iOS 多屏显示 - 深入了解
description: 本文档更深入地探讨了扩展的 Phoneword 应用程序，进一步介绍了模型-视图-控制器、iOS 导航和其他 iOS 开发概念。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: c866e5f4-8154-4342-876e-efa0693d66f5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 3bcfb20d8283f621ac1d32730ee67be2b09efe50
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918099"
---
# <a name="hello-ios-multiscreen--deep-dive"></a>深入了解 iOS 多屏显示

在“快速入门”演练中，我们生成并运行了首个多屏显示的 Xamarin.iOS 应用程序。 现在就来更深入了解 iOS 导航和体系结构。

在本指南中，将介绍模型-视图-控制器 (MVC) 模式以及其在 iOS 体系结构和导航中的角色。
然后将深入探讨导航控制器，并学习如何使用它在 iOS 中提供熟悉的导航体验。

## <a name="model-view-controller-mvc"></a>模型视图控制器 (MVC)

在[了解 iOS](~/ios/get-started/hello-ios/index.md) 教程中，介绍了 iOS 应用程序只有一个“窗口”，且视图控制器负责将其“内容视图层次结构”加载到窗口中。 如下图所示，在第二个 Phoneword 演练中，我们向应用程序添加了第二个屏幕，并在两个屏幕间传递了一些数据（一份电话号码列表）：

 [![](hello-ios-multiscreen-deepdive-images/08.png "This diagram illustrates passing data between two screens")](hello-ios-multiscreen-deepdive-images/08.png#lightbox)

本例中，在第一个屏幕中收集数据，然后从第一个视图控制器传递到第二个，由第二个屏幕显示。 屏幕、视图控制器和数据的分布方式遵循模型-视图-控制器 (MVC) 模式。 在后面几节中，我们将讨论该模式的优势及其组件，并探讨如何将其应用到 Phoneword 应用程序中。

### <a name="benefits-of-the-mvc-pattern"></a>MVC 模式的优势

“模型-视图-控制器”是一种设计模式，一种可重用的体系结构解决方案，用于处理代码中的常见问题或用例。 MVC 是应用程序的体系结构，具有图形用户界面 (GUI)。 它将向应用程序中的对象分配以下三种角色之一：模型（数据或应用程序逻辑）、视图（用户界面）和控制器（代码后置）。 下图描述 MVC 模式的 3 个部分与用户之间的关系：

 [![](hello-ios-multiscreen-deepdive-images/00.png "This diagram illustrates the relationships between the three pieces of the MVC pattern and the user")](hello-ios-multiscreen-deepdive-images/00.png#lightbox)

MVC 模式很有用，因为它在 GUI 应用程序的不同部件之间实现逻辑分离，简化了代码和视图的重复使用。 让我们直接进入，更详细地分别查看这三种角色。

> [!NOTE]
> MVC 模式有些类同于 ASP.NET 网页或 WPF 应用程序的结构。 在这些示例中，“视图”是一种组件，实际负责描述 UI，并响应 ASP.NET 中的 ASPX (HTML) 网页或 WPF 应用中的 XAML。 “控制器”是负责管理“视图”的组件，它对应于 ASP.NET 或 WPF 中的代码后置。

### <a name="model"></a>“模型”

“模型”对象通常是特定于应用程序的数据表示形式，其将在“视图”中显示或输入到“视图”中。 “模型”定义通常不严格 - 例如，在 **Phoneword_iOS** 应用中，电话号码列表（表现为字符串列表）就是“模型”。 如果正在构建跨平台应用程序，可选择在 iOS 和 Android 应用程序之间共享 **PhonewordTranslator** 代码。 也可将该共享代码视为“模型”。

MVC 完全不考虑数据持久性和模型的访问权限。 换言之，MVC 不在意数据看起来怎样或者如何存储，仅在乎数据的表示形式。 例如，可选择在 SQL 数据库中存储数据，或将其保留在某个云存储机制中，还可仅使用 `List<string>`。 为实现 MVC，模式中只包含数据表示形式本身。

在某些情况下，MVC 的“模型”部分可能为空。 例如，可能会选择向应用添加一些静态页面，用于解释电话转换器的工作原理、其构建原因以及如何联系我们以报告 bug。 这些应用屏幕仍将使用“视图”和“控制器”进行创建，但其不包含任何真实的“模型”数据。

> [!NOTE]
> 在某些宣传资料中，MVC 模式的“模型”部分可指代整个应用程序后端，而不仅仅是 UI 上显示的数据。 在本指南中，我们使用该模型的新型解释，但区别并不特别重要。

### <a name="view"></a>查看

“视图”是负责呈现用户界面的组件。 在几乎所有使用 MVC 模式的平台中，用户界面由各层视图组成。 可将 MVC 中的“视图”视为视图层次结构，该结构具有单视图（称为根视图且位于层次结构顶部）和任意数量的次要视图（称为子视图且位于单视图下方）。 在 iOS 中，屏幕的“内容视图层次结构”对应 MVC 中的“视图”组件。

### <a name="controller"></a>控制器

“控制器”对象是一种组件，可将所有内容连在一起并通过 `UIViewController` 在 iOS 中呈现。 可将“控制器”想象成屏幕或一组视图的支持代码。 “控制器”负责侦听来自用户的请求并返回相应的视图层次结构。 它侦听来自视图（按钮点击、文本输入等）的请求，并执行相应的处理、“视图”调整和“视图”重载。 “控制器”还负责根据应用程序中的任意支持数据存储创建或检索“模型”并使用其数据填充“视图”。

“控制器”还可管理其他“控制器”。 例如，“控制器”还可在需要显示其他屏幕时加载其他“控制器”，或者管理一组“控制器”以监视彼此间的顺序和转换。 在下一节中，我们将举例说明管理其他“控制器”的控制器，同时介绍名为导航控制器的特殊类型的 iOS 视图控制器。

## <a name="navigation-controller"></a>导航控制器

在 Phoneword 应用程序中，我们使用导航控制器来帮助管理多个屏幕之间的导航。 导航控制器是一个按 `UIViewController` 类表示的专用 `UINavigationController`。 导航控制器不会管理单个内容视图层次结构，转而管理其他视图控制器及其按导航工具栏形式显示的特定内容视图层次结构（包括标题、后退按钮和其他可选功能）。

导航控制器在 iOS 应用程序中很常见，为主要的 iOS 应用程序（如“设置”应用）提供导航，如以下屏幕截图所示：

 [![](hello-ios-multiscreen-deepdive-images/01.png "The navigation controller provides navigation for iOS applications like the Settings app shown here")](hello-ios-multiscreen-deepdive-images/01.png#lightbox)

导航控制器有 3 大主要功能：

- **提供用于向前导航的 Hook** – 导航控制器在内容视图层次结构推送到导航堆栈的情况下使用分层导航手法。 可将导航堆栈视为一叠扑克牌，只有最上面的牌可见，如下图所示：  

    [![](hello-ios-multiscreen-deepdive-images/02.png "This diagram illustrates navigation as a stack of cards")](hello-ios-multiscreen-deepdive-images/02.png#lightbox)

- **选择性提供后退按钮** - 将新项目推送到导航堆栈时，标题栏可自动显示后退按钮，允许用户向后导航。 按后退按钮弹出导航堆栈中的当前视图控制器，并将先前的内容视图层次结构加载到窗口中：  

    [![](hello-ios-multiscreen-deepdive-images/03.png "This diagram illustrates 'popping' a card off the stack")](hello-ios-multiscreen-deepdive-images/03.png#lightbox)

- **提供标题栏** - 导航控制器的顶部称为标题栏。 它负责显示视图控制器标题，如下图所示：  

    [![](hello-ios-multiscreen-deepdive-images/04.png "The Title Bar is responsible for displaying the view controller title")](hello-ios-multiscreen-deepdive-images/04.png#lightbox)

### <a name="root-view-controller"></a>根视图控制器

导航控制器不会管理内容视图层次结构，因此本身没有要显示的内容。
相反，导航控制器与根视图控制器进行配对：

 [![](hello-ios-multiscreen-deepdive-images/05.png "A navigation controller is paired with a Root view controller")](hello-ios-multiscreen-deepdive-images/05.png#lightbox)

根视图控制器是指导航控制器堆栈中的第一个视图控制器，而且根视图控制器的内容视图层次结构是指要加载到窗口的第一个内容视图层次结构。 如果要在导航控制器堆栈上放置整个应用程序，可将无源的 Segue 迁移到导航控制器，然后将第一个屏幕的视图控制器设为根视图控制器，操作方式与在 Phoneword 应用中执行的一样：

 [![](hello-ios-multiscreen-deepdive-images/06.png "The Sourceless Segue sets the first screens view controller as the Root view controller")](hello-ios-multiscreen-deepdive-images/06.png#lightbox)

### <a name="additional-navigation-options"></a>其他导航选项

导航控制器是在 iOS 中处理导航的常见方式，但不是唯一的选择。 例如，[选项卡栏控制器](~/ios/user-interface/controls/creating-tabbed-applications.md)可将应用程序拆分为不同功能区域，[拆分视图控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/split_view/use_split_view_to_show_two_controllers)可用于创建母版/详细信息视图。 将导航控制器与这些其他导航模式结合使用，可采用多种方式灵活显示和导航 iOS 中的内容。

## <a name="handling-transitions"></a>处理转换

在 Phoneword 演练中，我们按两种方式处理两种视图控制器之间的转换 – 先使用情节提要 Segue，然后是编程方式。 让我们详细了解这两种选项。

### <a name="prepareforsegue"></a>PrepareForSegue

使用“显示”操作向情节提要添加 Segue 时，指示 iOS 将第二个视图控制器推送到导航控制器的堆栈：

 [![](hello-ios-multiscreen-deepdive-images/09.png "Setting the segue type from a dropdown list")](hello-ios-multiscreen-deepdive-images/09.png#lightbox)

将 Segue 添加到情节提要即可创建屏幕之间的简单转换。 如果要传递视图控制器之间的数据，必须重写 `PrepareForSegue` 方法并自行处理数据：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);
    ...
}
```

iOS 在即将转换前调用 `PrepareForSegue`，然后将情节提要中创建的 Segue 传递给该方法。
此时，必须手动设置 Segue 的目标视图控制器。 以下代码可获取目标视图控制器的句柄，并将其强制转换为适当的类 - 本例中为 CallHistoryController：

```csharp
CallHistoryController callHistoryController = segue.DestinationViewController as CallHistoryController;
```

最后，通过将 `ViewController` 的 `CallHistoryController` 属性设为已拨号电话号码列表，将电话号码列表（模型）从 `PhoneHistory` 传递到 `CallHistoryController`：

```csharp
callHistoryController.PhoneNumbers = PhoneNumbers;
```

下面是使用 Segue 传递数据的完整代码：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController as CallHistoryController;

    if (callHistoryController != null) {
         callHistoryController.PhoneNumbers = PhoneNumbers;
    }
 }
```

### <a name="navigation-without-segues"></a>不使用 Segue 进行导航

若要在代码中切换不同的视图控制器，则无论是否使用 Segue，操作都相同，但需手动完成几个步骤。
首先，使用 `this.NavigationController` 获取对导航控制器的引用，我们当前位于该控制器堆栈中。 然后，使用导航控制器的 `PushViewController` 方法，将下一个视图控制器手动推送到堆栈（即传入视图控制器），还可对转换进行动画处理（将其设为 `true`）。

以下代码处理从 Phoneword 屏幕到“调用历史记录”屏幕的转换：

```csharp
this.NavigationController.PushViewController (callHistory, true);
```

若要实现到下一个视图控制器的转换，必须在情节提要中手动将其实例化，方式是调用 `this.Storyboard.InstantiateViewController` 并传入 `CallHistoryController` 的情节提要 ID：

```csharp
CallHistoryController callHistory =
this.Storyboard.InstantiateViewController
("CallHistoryController") as CallHistoryController;
```

最后，通过将 `ViewController` 的 `CallHistoryController` 属性设为已拨号电话号码列表，将电话号码列表（模型）从 `PhoneHistory` 传递到 `CallHistoryController`，操作方式与使用 Segue 处理转换时的一样：

```csharp
callHistory.PhoneNumbers = PhoneNumbers;
```

以下是编程转换的完整代码：

```csharp
CallHistoryButton.TouchUpInside += (object sender, EventArgs e) => {
    // Launches a new instance of CallHistoryController
    CallHistoryController callHistory = this.Storyboard.InstantiateViewController ("CallHistoryController") as CallHistoryController;
    if (callHistory != null) {
     callHistory.PhoneNumbers = PhoneNumbers;
     this.NavigationController.PushViewController (callHistory, true);
    }
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

Phoneword 应用程序引入了多个本指南中未提及的概念。 这些概念包括：

- **视图控制器的自动创建** – 在“属性面板”中输入视图控制器的类名称时，iOS 设计器将检查该类是否存在并生成视图控制器支持的类。 若要深入了解此选项以及其他 iOS 设计器功能，请参阅 [iOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南。
- **表格视图控制器** - `CallHistoryController` 是一种表格视图控制器。 表格视图控制器包含表格视图、iOS 中最常见的布局和数据显示工具。 但是，表格不在本指南的讨论范围内。 有关表格视图控制器的详细信息，请参阅[使用表格和单元格](~/ios/user-interface/controls/tables/index.md)指南。
- **情节提要 ID** – 通过设置情节提要 ID，可在 Objective-C 中创建“视图控制器”类，其中包含情节提要中视图控制器的代码后置。 情节提要 ID 用于查找 Objective-C 类并在情节提要中执行视图控制器的实例化。 有关情节提要 ID 的详细信息，请参阅[情节提要简介](~/ios/user-interface/storyboards/index.md)指南。

## <a name="summary"></a>总结

祝贺，你完成了首个多屏显示的 iOS 应用程序！

在本指南中，我们介绍了 MVC 模式并用其创建多屏应用程序。 我们还探讨了导航控制器及其在支持 iOS 导航方面的角色。 现在，你了解了丰富的基础知识，可开始自行开发 Xamarin.iOS 应用程序。

接下来，让我们借助[移动开发简介](~/cross-platform/get-started/introduction-to-mobile-development.md)和[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)指南，学习使用 Xamarin 构建跨平台应用程序。

## <a name="related-links"></a>相关链接

- [Hello，iOS（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [iOS 人机界面指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS 预配门户](https://developer.apple.com/ios/manage/overview/index.action)
