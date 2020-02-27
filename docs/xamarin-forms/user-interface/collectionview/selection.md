---
title: Xamarin CollectionView 选择
description: 默认情况下，CollectionView 选择处于禁用状态。 但是，可以启用单个和多个选择。
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a4cc237ef738edeccf66f1a91a010e4831c1c72f
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635623"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin CollectionView 选择

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义控制项选择的下列属性：

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)，类型[`SelectionMode`](xref:Xamarin.Forms.SelectionMode)，选择模式。
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)，类型 `object`，列表中选定的项。 此属性的默认绑定模式为 `TwoWay`，在未选择任何项时具有 `null` 值。
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)，类型 `IList<object>`，列表中选定的项。 此属性的默认绑定模式为 `OneWay`，在未选择任何项时具有 `null` 值。
- `ICommand`类型的[`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)，当选定项发生更改时，将执行该类型。
- `object`类型的[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)，它是传递到 `SelectionChangedCommand`的参数。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView)选择已禁用。 但是，可以通过将[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性值设置为[`SelectionMode`](xref:Xamarin.Forms.SelectionMode)枚举成员之一来更改此行为：

- `None` –表示无法选择项。 这是默认值。
- `Single` –指示可以选择单个项，突出显示选定项。
- `Multiple` –指示可以选择多个项，并且突出显示选定项。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件，该事件在[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性发生更改时引发，无论用户是从列表中选择项，还是应用程序设置属性。 此外，当[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性发生更改时，也会触发此事件。 `SelectionChanged` 事件附带的[`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs)对象具有两个属性，两者都是 `IReadOnlyList<object>`类型：

- `PreviousSelection` –选定内容更改之前选定的项的列表。
- `CurrentSelection` –选定内容更改后选定的项的列表。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有一个 `UpdateSelectedItems` 方法，该方法使用选定项的列表更新[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性，而只引发单个更改通知。

## <a name="single-selection"></a>单项选择

[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为 `Single`时，可以选择[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的单个项。 选择某项时， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将设置为所选项的值。 此属性发生更改时，将执行[`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) （其中包含传递给 `ICommand`的[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)的值），并激发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

下面的 XAML 示例显示了一个可响应单项选择的[`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此代码示例中，当引发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件，并且事件处理程序检索先前选定的项以及当前选定项时，将执行 `OnCollectionViewSelectionChanged` 事件处理程序：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件可以由更改[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性后发生的更改触发。

以下屏幕截图显示了[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的单个项选择：

[![在 iOS 和 Android 上进行单项选择的 CollectionView 垂直列表屏幕截图](selection-images/single-selection.png "带有单项选择的 CollectionView 垂直列表")](selection-images/single-selection-large.png#lightbox "带有单项选择的 CollectionView 垂直列表")

## <a name="multiple-selection"></a>多重选择

[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为 `Multiple`时，可以选择[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的多个项。 选择项时， [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性将设置为所选的项。 此属性发生更改时，将执行[`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) （其中包含传递给 `ICommand`的[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)的值），并激发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

下面的 XAML 示例显示了一个可响应多项选择的[`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此代码示例中，当引发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件，并且事件处理程序检索先前选定的项以及当前选定项时，将执行 `OnCollectionViewSelectionChanged` 事件处理程序：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件可以由更改[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性后发生的更改触发。

以下屏幕截图显示了[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的多个项目选择：

[![在 iOS 和 Android 上具有多个选定内容的 CollectionView 垂直列表的屏幕截图](selection-images/multiple-selection.png "具有多个选定内容的 CollectionView 垂直列表")](selection-images/multiple-selection-large.png#lightbox "具有多个选定内容的 CollectionView 垂直列表")

## <a name="single-pre-selection"></a>单项选择

[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为 `Single`时，可以通过将[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性设置为项，来预选择[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的单个项。 下面的 XAML 示例显示了预先选择单个项的 `CollectionView`：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性的默认绑定模式为 `TwoWay`。

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性数据绑定到 `Monkey`类型的已连接视图模型的 `SelectedMonkey` 属性。 默认情况下，使用 `TwoWay` 绑定，以便在用户更改所选的项时，`SelectedMonkey` 属性的值将设置为选定的 `Monkey` 对象。 `SelectedMonkey` 属性是在 `MonkeysViewModel` 类中定义的，并且设置为 `Monkeys` 集合的第四项：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

因此，当显示[`CollectionView`](xref:Xamarin.Forms.CollectionView)时，将预先选择列表中的第四项：

[![IOS 和 Android 上包含单项选择的 CollectionView 垂直列表屏幕截图](selection-images/single-pre-selection.png "带有单项选择的 CollectionView 垂直列表")](selection-images/single-pre-selection-large.png#lightbox "带有单项选择的 CollectionView 垂直列表")

## <a name="multiple-pre-selection"></a>多个预选择

[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为 `Multiple`时，可以预先选择[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的多个项。 下面的 XAML 示例显示了一个 `CollectionView`，它将启用多个项的预选择：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性的默认绑定模式为 `OneWay`。

[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性数据绑定到 `ObservableCollection<object>`类型的已连接视图模型的 `SelectedMonkeys` 属性。 `SelectedMonkeys` 属性是在 `MonkeysViewModel` 类中定义的，并设置为 `Monkeys` 集合中的第二、第四和第五项：

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

因此，当显示[`CollectionView`](xref:Xamarin.Forms.CollectionView)时，将预先选择列表中的第二个、第四个和第五个项：

[![在 iOS 和 Android 上具有多个预选择的 CollectionView 垂直列表屏幕截图](selection-images/multiple-pre-selection.png "具有多个预选择的 CollectionView 垂直列表")](selection-images/multiple-pre-selection-large.png#lightbox "具有多个预选择的 CollectionView 垂直列表")

## <a name="clear-selections"></a>清除选择

可以通过将[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)和[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性设置为 `null`，或将其绑定到的对象清除。

## <a name="change-selected-item-color"></a>更改选定项的颜色

[`CollectionView`](xref:Xamarin.Forms.CollectionView)具有一个 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用于对 `CollectionView`中的选定项启动视觉对象更改。 此 `VisualState` 的一个常见用例是更改选定项的背景色，如下面的 XAML 示例中所示：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> 包含 `Selected` `VisualState` 的[`Style`](xref:Xamarin.Forms.Style)必须具有[`TargetType`](xref:Xamarin.Forms.Style.TargetType)属性值，该属性值将设置为`DataTemplate`属性值，该属性的值为[`ItemTemplate`](xref:Xamarin.Forms.DataTemplate)的根元素的类型。

在此示例中， [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType)属性值设置为 `Grid`，因为[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)的根元素是一个[`Grid`](xref:Xamarin.Forms.Grid)。 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState)指定在选择[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的项时，该项的[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)将设置为 `LightSkyBlue`：

[![在 iOS 和 Android 上使用自定义单项选择颜色的 CollectionView 垂直列表屏幕截图](selection-images/single-selection-color.png "使用自定义单项选择颜色的 CollectionView 垂直列表")](selection-images/single-selection-color-large.png#lightbox "使用自定义单项选择颜色的 CollectionView 垂直列表")

有关可视状态的详细信息，请参阅[Xamarin。窗体可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-selection"></a>禁用选定内容

默认情况下，禁用[`CollectionView`](xref:Xamarin.Forms.CollectionView)选项。 但是，如果 `CollectionView` 启用了选择，则可以通过将[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为 `None`来禁用它：

```xaml
<CollectionView ...
                SelectionMode="None" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

如果将[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为 `None`，则[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的项不能选择， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将保持 `null`，并且不会激发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

> [!NOTE]
> 如果已选择某项并且[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性从 `Single` 更改为 `None`，则[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将设置为 `null` 并将使用空的`SelectionChanged`属性激发[`CurrentSelection`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. Forms 视觉对象状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)
