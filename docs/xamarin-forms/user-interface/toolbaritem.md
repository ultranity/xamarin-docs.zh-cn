---
title: Xamarin. Forms ToolbarItem
description: ToolbarItem 类是应用程序导航栏中使用的一种特殊类型的按钮。
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: afdf9029f836ac8e55b2bb338b31f669af946c12
ms.sourcegitcommit: 6d86aac422d6ce2131930d18ada161d117c8c61b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77567088"
---
# <a name="xamarinforms-toolbaritem"></a>Xamarin. Forms ToolbarItem

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

Xamarin [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)类是一种特殊类型的按钮，可将其添加到 `Page` 对象的 `ToolbarItems` 集合。 每个 `ToolbarItem` 对象将作为按钮显示在应用程序的导航栏中。 `ToolbarItem` 实例可以有一个图标，并显示为主菜单项或辅助菜单项。 `ToolbarItem` 类继承自[`MenuItem`](xref:Xamarin.Forms.MenuItem)。

以下屏幕截图显示了 iOS 和 Android 上导航栏中 `ToolbarItem` 对象：

!["Android 和 iOS 上的 ToolbarItem 演示屏幕截图"](toolbaritem-images/toolbaritem-device-screenshot.png "Android 和 iOS 上的 ToolbarItem 演示屏幕截图")

`ToolbarItem` 类定义以下属性：

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)是 `ToolbarItemOrder` 的枚举值，用于确定 `ToolbarItem` 实例是否显示在主菜单或辅助菜单中。
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)是一个 `integer` 值，该值确定 `Page` 对象的 `ToolbarItems` 集合中项的显示顺序。

`ToolbarItem` 类继承了 `MenuItem` 类中通常使用的以下属性：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是一种允许将用户操作（如指指点击或单击）绑定到 viewmodel 上定义的命令的 `ICommand`。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是指定应传递到 `Command`的参数的 `object`。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)是确定 `ToolbarItem` 对象上显示图标的 `ImageSource` 值。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)是确定 `ToolbarItem` 对象上的显示文本的 `string`。

这些属性由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持，因此 `ToolbarItem` 实例可以是数据绑定的目标。

> [!NOTE]
> 从[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)对象创建工具栏的另一种方法是将[`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)附加属性设置为包含多个视图的布局类。 有关详细信息，请参阅[在导航栏中显示视图](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar)。

## <a name="create-a-toolbaritem"></a>创建 ToolbarItem

`ToolbarItem` 对象可以在 XAML 中实例化。 可以设置 `Text` 和 `IconImageSource` 属性，以确定按钮在导航栏中的显示方式。 下面的示例演示如何使用一些公共属性集来实例化 `ToolbarItem`，并将其添加到 `ContentPage`的 `ToolbarItems` 集合：

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

此示例将生成一个包含文本、一个图标并且在主导航栏区域中首先出现的 `ToolbarItem` 对象。 也可以在代码中创建 `ToolbarItem`，并将其添加到 `ToolbarItems` 集合：

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

每个平台项目中必须存在由 `string`表示的文件（作为 `IconImageSource` 属性提供）。

> [!NOTE]
> 每个平台上的图像资产的处理方式不同。 `ImageSource` 可以来自源，包括本地文件或嵌入的资源、URI 或流。 有关在 Xamarin. Forms 中设置 `IconImageSource` 属性和图像的详细信息，请参阅[xamarin 中的图像](~/xamarin-forms/user-interface/images.md)。

## <a name="define-button-behavior"></a>定义按钮行为

`ToolbarItem` 类继承 `MenuItem` 类中的 `Clicked` 事件。 事件处理程序可以附加到 `Clicked` 事件，以响应 `ToolbarItem` XAML 中的实例：

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

事件处理程序也可以在代码中附加：

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

前面的示例引用 `OnItemClicked` 事件处理程序。 下面的代码演示实现示例：

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem` 对象还可以使用 `Command` 和 `CommandParameter` 属性来响应用户输入，而无需使用事件处理程序。 有关 `ICommand` 接口和 MVVM 数据绑定的详细信息，请参阅[Xamarin。窗体 MENUITEM MVVM 行为](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm)。

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>在运行时启用或禁用 ToolbarItem

若要启用在运行时禁用 `ToolbarItem`，请将其 `Command` 属性绑定到 `ICommand` 实现，并确保 `canExecute` 委托根据需要启用和禁用 `ICommand`。

有关详细信息，请参阅[在运行时启用或禁用 MenuItem](menuitem.md#enable-or-disable-a-menuitem-at-runtime)。

## <a name="primary-and-secondary-menus"></a>主菜单和辅助菜单

`ToolbarItemOrder` 枚举具有 `Default`、`Primary`和 `Secondary` 值。

`Order` 属性设置为 `Primary`时，`ToolbarItem` 对象将显示在所有平台上的主导航栏中。 `ToolbarItem` 对象优先于页面标题，这将被截断以为项目腾出空间。 以下屏幕截图显示 iOS 和 Android 上主菜单中的 `ToolbarItem` 对象：

!["ToolbarItem 主菜单屏幕快照 Android 和 iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Android 和 iOS 上的 ToolbarItem 主菜单屏幕截图")

`Order` 属性设置为 `Secondary`时，行为因平台而异。 在 UWP 和 Android 上，"`Secondary` 项" 菜单显示为三个点，可以点击或单击这些点来显示垂直列表中的项。 在 iOS 上，"`Secondary` 项" 菜单显示在导航栏下面作为水平列表。 以下屏幕截图显示了 iOS 和 Android 上的辅助菜单：

!["ToolbarItem 辅助菜单屏幕快照 Android 和 iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Android 和 iOS 上的 ToolbarItem 辅助菜单屏幕截图")

> [!WARNING]
> `Order` 属性设置为 `Secondary` 的 `ToolbarItem` 对象中的图标行为在平台之间不一致。 避免在辅助菜单中显示的项上设置 "`IconImageSource`" 属性。

## <a name="related-links"></a>相关链接

* [ToolbarItem 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Xamarin 中的图像](~/xamarin-forms/user-interface/images.md)
* [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
