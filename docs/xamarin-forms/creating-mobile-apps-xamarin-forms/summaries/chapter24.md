---
title: 第 24 章的摘要。 页导航
description: 使用 Xamarin.Forms 创建移动应用： 第 24 章的摘要。 页导航
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77130834"
---
# <a name="summary-of-chapter-24-page-navigation"></a>第 24 章的摘要。 页导航

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

许多应用程序包含多个用户在其之间导航的页面。 应用程序始终*具有主页或* *主页*，并从该应用程序导航到其他页，这些页在堆栈中进行了定位以便向后定位。 第25章介绍了其他导航选项[ **。页面种类**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>模式页面和无模式页

`VisualElement` 定义类型[`INavigation`](xref:Xamarin.Forms.INavigation)的[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)属性，其中包括以下两个用于导航到新页面的方法：

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

这两种方法都接受 `Page` 实例作为参数，并返回一个 `Task` 对象。 以下两种方法导航回前一页：

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

如果用户界面具有其自己的 "**后退**" 按钮（如 Android 和 Windows phone），则应用程序不需要调用这些方法。

尽管这些方法可从任何 `VisualElement`获取，但通常从当前 `Page` 实例的 `Navigation` 属性中调用它们。

当用户需要提供的页上某些信息，然后返回到前一页时，应用程序通常使用模式页面。 不是模式的页有时称为无模式或*层次结构*。 在页面本身中为 nothing 使它作为模式对话框或无模式;它受到改为用来导航到它的方法。 若要在所有平台上工作，模式页面必须提供其自己的用户界面导航回前一页。

[**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)示例允许浏览无模式页面和模式页面之间的差异。 使用页面导航的任何应用程序都必须将其主页传递到[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)构造函数（通常在程序的 `App` 类中）。 一个优点是，你不再需要在 iOS 页面上设置 `Padding`。

你将发现，对于无模式页，将显示该页的[`Title`](xref:Xamarin.Forms.Page.Title)属性。 在 iOS、 Android 和 Windows 平板电脑和桌面平台所有提供的用户界面元素导航回前一页。 当然，Android 和 Windows phone 设备都有一个标准的 "**后退**" 按钮。

对于模式页，不显示该页 `Title`，且不提供任何用户界面元素以返回到上一页。 尽管可以使用 Android 和 Windows phone 标准版**后退**按钮返回到上一页，但其他平台上的模式页必须提供自己的机制才能返回。

### <a name="animated-page-transitions"></a>经过动画处理的页面过渡

如果希望页面过渡包含动画，则会随第二个布尔 `true` 参数一起提供不同导航方法的替代版本：

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

不过，默认情况下，标准页面导航方法包含动画，因此，这些方法只是在启动时导航到特定页面（如本章末尾所述）或提供自己的入口动画（如 Chapter22 中所述） [ **。动画**](chapter22.md)）。

### <a name="visual-and-functional-variations"></a>可视化和功能的变体

`NavigationPage` 包括在 `App` 方法中实例化类时可以设置的两个属性：

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` 还包括四个附加的可绑定属性，这些属性会影响其设置的特定页面：

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) 和 [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) 和 [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- 仅在 iOS 上[`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String))和[`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject))工作
- 仅在 iOS 和 Android 上[`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource))和[`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject))工作

### <a name="exploring-the-mechanics"></a>探索机制

页面导航方法都是异步的，并且应该与 `await`一起使用。 完成并不表示页面导航已完成，但仅限安全检查页面导航堆栈。

当一个页面导航到另一个页面时，第一页通常会调用其[`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)方法，第二页获取对其[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)方法的调用。 同样，当一个页面返回到另一个页面时，第一页将获取对其 `OnDisappearing` 方法的调用，第二页通常获取对其 `OnAppearing` 方法的调用。 这些调用 （和调用导航时的异步方法完成） 的顺序是依赖于平台。 使用"一般"中的两个上述语句字是由于 Android 模式页面导航，这些方法调用不会发生。

此外，对 `OnAppearing` 和 `OnDisappearing` 方法的调用不一定表示页面导航。

`INavigation` 接口包含两个集合属性，可用于检查导航堆栈：

- 无模式堆栈的类型 `IReadOnlyList<Page>` 的[`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)
- 模式堆栈的类型 `IReadOnlyList<Page>` 的[`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)

最安全的方法是从 `NavigationPage` 的 `Navigation` 属性（应是 `App` 类的[`MainPage`](xref:Xamarin.Forms.Application.MainPage)属性）访问这些堆栈。 才是安全的异步页面导航方法完成后检查这些堆栈。 如果当前页是一个模式页，则 `NavigationPage` 的[`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage)属性不指示当前页，而是指示最后一个无模式页面。

[**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)示例使你可以浏览页面导航和堆栈以及法律类型的页面导航：

- 无模式页面可以导航到另一个无模式页面或模式页面
- 模式页面可以仅向另一个模式页面导航

### <a name="enforcing-modality"></a>强制模式

需要从用户获取某些信息时，应用程序使用模式的页面。 用户应禁止前提供该信息返回到以前的页面。 在 iOS 上，可以轻松地提供 "**后退**" 按钮，仅当用户已完成该页时才启用它。 但对于 Android 和 Windows phone 设备，应用程序应该覆盖[`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed)方法，并返回 `true` 如果程序已经处理了 "**后退**" 按钮，如[**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)示例中所示。

[**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)示例演示了如何在 MVVM 方案中运行。

## <a name="navigation-variations"></a>导航变体

如果特定的模式页面可以导航到多个时间，它应保留的信息，以便用户可以编辑信息，而不用键入所有在再次。 可以通过保留的模式页面，该特定实例来处理此但更好的方法 （尤其是在 iOS 上） 保留视图模型中的信息。

### <a name="making-a-navigation-menu"></a>使导航菜单

[**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)示例演示如何使用 `TableView` 列出菜单项。 每个项目都与特定页面的 `Type` 对象相关联。 选择该项目时，程序实例化页并导航到它。

[![视图库类型的三个屏幕截图](images/ch24fg21-small.png "TableView 列表菜单项")](images/ch24fg21-large.png#lightbox "TableView 列表菜单项")

[**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)示例略有不同，因为菜单包含每个页面的实例，而不是类型的实例。 这可帮助保留每个页面中的信息，但所有页面必须在程序启动时实例都化。

### <a name="manipulating-the-navigation-stack"></a>操作导航堆栈

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)演示了一些由 `INavigation` 定义的函数，这些函数使你能够以结构化的方式操作导航堆栈：

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- 带有可选动画的[`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)和[`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean))

### <a name="dynamic-page-generation"></a>动态页生成

[**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)示例演示如何在运行时基于用户输入构造页面。

## <a name="patterns-of-data-transfer"></a>数据传输模式

经常需要在 &mdash; 页面之间共享数据，以便将数据传输到导航页面，并将数据返回到调用它的页面。 有几种方法执行此操作。

### <a name="constructor-arguments"></a>构造函数自变量

导航到新页面，时，可以实例化具有允许初始化其自身的页的构造函数自变量的页类。 [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)示例演示了这一点。 导航页面还可以通过导航到该页面的页面设置其 `BindingContext`。

### <a name="properties-and-method-calls"></a>属性和方法调用

其余的数据传输示例探索之间页一页导航到另一个页面时来回传递信息的问题。 在这些讨论中，*主页*导航到 "*信息*" 页，并且必须将初始化的信息传输到 "*信息*" 页。 "*信息*" 页获取用户的其他信息，并将信息传输到*主页*。

在实例化该页后，*主页*可以轻松地访问 "*信息*" 页中的公共方法和属性。 "*信息*" 页还可以访问*主页*中的公共方法和属性，但为此选择合适的时间可能比较棘手。 [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)示例在其 `OnDisappearing` 重写中执行此功能。 一个缺点是*信息*页需要知道*主页*的类型。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)类为两个页面提供了另一种相互通信的方式。 消息的文本字符串由标识和伴随的任何对象。

希望接收特定类型消息的程序必须使用[`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)订阅这些消息，并指定回调函数。 稍后，它可以通过调用[`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)取消订阅。 回调函数接收从指定的类型发送的任何消息，该消息具有通过[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法发送的指定名称。

[**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程序演示了如何使用消息中心传输数据，但这要求*信息*页知道*主页*的类型。

### <a name="events"></a>事件

该事件是一个类以将信息发送到另一个类，而不必知道该类的类型的历史悠久方法。 在[**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)示例中， *info*类定义一个事件，该事件在信息准备就绪时触发。 但是，不会在*主页*上方便地分离事件处理程序。

### <a name="the-app-class-intermediary"></a>应用程序类中介

[**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)示例演示如何使用 "*主页*" 页和 "*信息*" 页访问 `App` 类中定义的属性。 这是一个不错的解决方案，但下一节介绍了更好。

### <a name="switching-to-a-viewmodel"></a>切换到 ViewModel

使用 ViewModel 信息可让 "*主页*" 页和 *"信息" 页共享*信息类的实例。 [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)示例演示了这一点。

### <a name="saving-and-restoring-page-state"></a>保存和还原页面状态

如果在 "*信息*" 页处于活动状态时程序进入睡眠状态，应用程序必须保存信息时，`App` 类中介或 ViewModel 方法是理想选择。 [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)示例演示了这一点。

## <a name="saving-and-restoring-the-navigation-stack"></a>保存和还原导航堆栈

在一般情况下，进入睡眠状态的多页程序应导航到同一页面时还原。 这意味着此类程序应保存在导航堆栈的内容。 本部分介绍如何自动执行此过程中实现此目的而设计的类。 此类还会调用各个页面，让他们可以保存并还原其页面状态。

[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义了一个名为[`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)的接口，类可以实现该类来保存和还原 `Properties` 字典中的项。

**FormsBook**库中的[`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)类派生自 `Application`。 然后，你可以从 `MultiPageRestorableApp` 派生 `App` 类并执行一些内务处理。

[**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)演示如何使用 `MultiPageRestorableApp`。

### <a name="something-like-a-real-life-app"></a>类似于实际的应用

[**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)示例还利用 `MultiPageRestorableApp`，并允许输入和编辑保存在 `Properties` 字典中的注释。

## <a name="related-links"></a>相关链接

- [第24章全文（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第24章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [模式页](~/xamarin-forms/app-fundamentals/navigation/modal.md)
