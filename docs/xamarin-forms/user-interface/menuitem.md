---
title: Xamarin。窗体 MenuItem
description: MenuItem 类用于为菜单项（如 ListView 项上下文菜单和 Shell 应用程序飞出菜单）创建菜单项。
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: b4690feb6444405d090a0b2bafd6c8615b2ffa8b
ms.sourcegitcommit: 6d86aac422d6ce2131930d18ada161d117c8c61b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77567060"
---
# <a name="xamarinforms-menuitem"></a>Xamarin。窗体 MenuItem

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

Xamarin [`MenuItem`](xref:Xamarin.Forms.MenuItem)类定义菜单的菜单项，如 `ListView` 项上下文菜单和 Shell 应用程序飞出菜单。

以下屏幕截图显示 iOS 和 Android 上 `ListView` 上下文菜单中的 `MenuItem` 对象：

[!["IOS 和 Android 上的菜单项"](menuitem-images/menuitem-demo-cropped.png "IOS 和 Android 上的菜单项")](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 完整映像上的菜单项")

`MenuItem` 类定义以下属性：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是一种允许将用户操作（如指指点击或单击）绑定到 viewmodel 上定义的命令的 `ICommand`。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是指定应传递到 `Command`的参数的 `object`。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)是定义显示图标的 `ImageSource` 值。
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)是一个 `bool` 值，该值指示 `MenuItem` 是否从列表中删除其关联的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)是一个 `bool` 值，该值指示此对象是否响应用户输入。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)是指定显示文本的 `string` 值。

这些属性由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持，因此 `MenuItem` 实例可以是数据绑定的目标。

## <a name="create-a-menuitem"></a>创建 MenuItem

`MenuItem` 对象可在 `ListView` 对象项的上下文菜单中使用。 最常见的模式是在 `ViewCell` 实例中创建 `MenuItem` 对象，该实例用作 `ListView``ItemTemplate`的 `DataTemplate` 对象。 填充 `ListView` 对象时，它会使用 `DataTemplate`创建每个项，并在为项激活上下文菜单时公开 `MenuItem` 选项。

下面的示例演示 `ListView` 对象的上下文中 `MenuItem` 实例化：

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

还可以在代码中创建 `MenuItem`：

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>定义包含事件的 MenuItem 行为

`MenuItem` 类会公开 `Clicked` 事件。 可以将事件处理程序附加到此事件，以响应在 XAML 中单击或单击 `MenuItem` 实例：

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件处理程序也可以在代码中附加：

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

前面的示例引用 `OnItemClicked` 事件处理程序。 下面的代码演示实现示例：

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>通过 MVVM 定义 MenuItem 行为

`MenuItem` 类通过[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象和 `ICommand` 接口支持模型-视图-VIEWMODEL （MVVM）模式。 以下 XAML 显示了绑定到 viewmodel 上定义的命令的 `MenuItem` 实例：

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

在前面的示例中，两个 `MenuItem` 对象均由其 `Command` 和绑定到 viewmodel 上命令的 `CommandParameter` 属性定义。 Viewmodel 包含 XAML 中引用的命令：

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

示例应用程序包括一个 `DataService` 类，该类用于获取用于填充 `ListView` 对象的项的列表。 使用 `DataService` 类中的项来实例化 viewmodel，并将其设置为代码隐藏中的 `BindingContext`：

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 图标

> [!WARNING]
> `MenuItem` 对象只显示 Android 上的图标。 在其他平台上，只会显示 `Text` 属性指定的文本。

 图标是使用 `IconImageSource` 属性指定的。 如果指定了图标，则不会显示 `Text` 属性指定的文本。 以下屏幕截图显示了 Android 上带有图标的 `MenuItem`：

!["Android 上的 MenuItem 图标屏幕截图"](menuitem-images/menuitem-android-icon.png "Android 上菜单菜单的屏幕截图")

有关在 Xamarin 中使用图像的详细信息，请参阅[xamarin 中的图像](~/xamarin-forms/user-interface/images.md)。

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>在运行时启用或禁用 MenuItem

若要启用在运行时禁用 `MenuItem`，请将其 `Command` 属性绑定到 `ICommand` 实现，并确保 `canExecute` 委托根据需要启用和禁用 `ICommand`。

> [!IMPORTANT]
> 使用 `Command` 属性启用或禁用 `MenuItem`时，不要将 `IsEnabled` 属性绑定到另一个属性。

下面的示例演示一个 `MenuItem`，其 `Command` 属性绑定到名为 `MyCommand`的 `ICommand`：

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

`ICommand` 实现需要一个 `canExecute` 委托，该委托返回 `bool` 属性的值以启用和禁用 `MenuItem`：

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public ToolbarItemViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsToolbarItemEnabled);
    }
}
```

在此示例中，除非设置了 `IsMenuItemEnabled` 属性，否则将禁用 `MenuItem`。 发生这种情况时，将调用 `Command.ChangeCanExecute` 方法，这将导致重新计算 `MyCommand` 的 `canExecute` 委托。

## <a name="cross-platform-context-menu-behavior"></a>跨平台上下文菜单行为

上下文菜单在每个平台上以不同的方式进行访问和显示。

在 Android 上，通过长时间按列表项激活上下文菜单。 上下文菜单替换标题和导航栏区域，`MenuItem` 选项显示为水平按钮。

!["Android 上上下文菜单的屏幕截图"](menuitem-images/menuitem-android-icon.png "Android 上上下文菜单的屏幕截图")

在 iOS 上，通过在列表项上轻扫激活上下文菜单。 上下文菜单将显示在列表项上，并且 `MenuItems` 将显示为水平按钮。

!["IOS 上上下文菜单的屏幕截图"](menuitem-images/menuitem-ios-contextmenu.png "IOS 上上下文菜单的屏幕截图")

在 UWP 上，右键单击列表项即可激活上下文菜单。 上下文菜单将作为垂直列表显示在光标附近。

!["UWP 上的上下文菜单的屏幕截图"](menuitem-images/menuitem-uwp.png "UWP 上的上下文菜单的屏幕截图")

## <a name="related-links"></a>相关链接

* [MenuItem 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Xamarin 中的图像](~/xamarin-forms/user-interface/images.md)
