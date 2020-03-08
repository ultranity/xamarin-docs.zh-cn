---
title: Xamarin.Forms 触发器
description: 此文章介绍了如何使用 Xamarin.Forms 触发器来响应 XAML 的用户界面更改。 触发器允许你在根据事件或属性更改更改控件外观的 XAML 中以声明的方式表达操作。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: bf9c06dae0df7da1cc69a85d8436376494039959
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912088"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 触发器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

触发器允许你在根据事件或属性更改更改控件外观的 XAML 中以声明的方式表达操作。 此外，状态触发器是一组专门的触发器，定义了何时应该应用 [`VisualState`](xref:Xamarin.Forms.VisualState)。

可以直接分配控件触发器，或将其添加到页面级别或应用级别的资源词典中，以应用到多个控件。

## <a name="property-triggers"></a>属性触发器

简单的触发器可以完全在 XAML 中表达，向控件的触发器集合添加 `Trigger` 元素。
此示例显示了收到焦点时更改 `Entry` 颜色的触发器：

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
            <!-- multiple Setters elements are allowed -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

触发器声明的重要部件有：

- **TargetType** - 触发器适用的控件类型。

- **Property** - 要监视的控件上的属性。

- **Value** - 当针对监视的属性发生时，导致激活触发器的值。

- **Setter** - `Setter` 元素的集合，可进行添加且在满足触发条件时使用。 必须指定要设置的 `Property` 和 `Value`。

- **EnterActions 和 ExitActions** （未显示） - 编写于代码中，且可用于 `Setter` 之外（或者不是该元素）的元素。 它们[如下所述](#enteractions-and-exitactions)。

### <a name="applying-a-trigger-using-a-style"></a>使用 style 应用触发器

还可将触发器添加到控件、页面或应用程序 `ResourceDictionary` 中的 `Style` 声明。 下面的示例声明隐式 style（即未设置 `Key`）；也就是说，它应用于页面上的所有 `Entry` 控件。

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                    <!-- multiple Setters elements are allowed -->
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

## <a name="data-triggers"></a>数据触发器

数据触发器使用数据绑定来监视另一个控件，以导致调用 `Setter`。 设置 `Binding` 特性，而不是属性触发器中的 `Property` 特性，以监视指定值。

下面的示例使用数据绑定语法 `{Binding Source={x:Reference entry}, Path=Text.Length}`
这是我们引用另一个控件的属性的方式。 如果 `entry` 的长度为零，将激活触发器。 在此示例中，当输入为空时，触发器将禁用该按钮。

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
            <!-- multiple Setters elements are allowed -->
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> 对 `Path=Text.Length` 求值时，请务必提供目标属性的默认值（例如， `Text=""`），因为如不提供，它将为 `null`，且触发器将不按预期工作。

除了指定 `Setter`，你还可提供 [`EnterActions` 和 `ExitActions`](#enteractions-and-exitactions)。

## <a name="event-triggers"></a>事件触发器

`EventTrigger` 元素只需 `Event` 属性，例如，下面示例中的 `"Clicked"`。

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

请注意，没有 `Setter` 元素，而是对 `local:NumericValidationTriggerAction` 定义的类的引用，这要求在页面的 XAML 中声明 `xmlns:local`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

类本身可实现 `TriggerAction`，这表示它应提供 `Invoke` 方法的替代，每当发生触发事件时就会调用该方法。

触发器操作实现应该：

- 实现泛型 `TriggerAction<T>` 类，并且泛型参数对应于触发器将应用到的控件类型。 可以使用 `VisualElement` 之类的超类编写适用于多种控件的触发器操作，或指定 `Entry` 等控件类型。

- 替代 `Invoke` 方法 - 每当满足触发器条件时调用该方法。

- 声明触发器时，可选择公开能在 XAML 中设置的属性。 相关示例，请参阅随附的示例应用程序中的 `VisualElementPopTriggerAction` 类。

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

然后，通过 XAML 使用事件触发器：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

共享 `ResourceDictionary` 中的触发器时请小心，由于可在控件之间共享同一个实例，因此配置一次的任何状态都会应用到所有这些控件。

注意：事件触发器不支持[如下所述](#enteractions-and-exitactions)的 `EnterActions` 和 `ExitActions`。

## <a name="multi-triggers"></a>多触发器

`MultiTrigger` 外观类似于 `Trigger` 或 `DataTrigger`，只是可能有多个条件。 触发 `Setter` 前，所有条件必须为 true。

下面的示例是绑定到两个不同输入（`email` 和 `phone`）的按钮的触发器：

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>
    <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

`Conditions` 集合还可以包含 `PropertyCondition` 元素，如下所示：

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>生成“全部需要”的多触发器

仅当满足所有条件时，多触发器才会更新其控件。 针对“所有字段长度均为零”（例如所有输入必须完整的登录页）测试很棘手，因为你需要“其中 Text.Length > 0”的条件，但该条件无法在 XAML 中表达。

可以使用 `IValueConverter` 执行此操作。 下面的转换器代码可将 `Text.Length` 绑定转换为 `bool`，指示字段是否为空：

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

若要在多触发器中使用此转换器，首先请将其添加到页面的资源字典（以及自定义 `xmlns:local` 命名空间定义）：

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

XAML 如下所示。 请注意下面的示例与第一个触发器示例之间的差异：

- 该按钮默认设置为 `IsEnabled="false"`。
- 多触发器条件可使用转换器将 `Text.Length` 值转换为 `boolean`。
- 如果所有条件为 `true`，setter 可使按钮的 `IsEnabled` 属性为 `true`。

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

这些屏幕截图显示了上述两个多触发器示例之间的差异。 在屏幕的顶部，仅一个 `Entry` 中的文本输入便足以启用“保存”按钮  。
在屏幕的底部，“登录”按钮在两个字段均包含数据迁保持非活动状态  。

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

发生触发器时实现更改的另一方式是通过添加 `EnterActions` 和 `ExitActions` 集合，并指定 `TriggerAction<T>` 实现。

[`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) 集合用于定义将在满足触发条件时调用的 [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) 对象的 `IList`。 [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) 集合用于定义将在不再满足触发条件后调用的 `TriggerAction` 对象的 `IList`。

> [!NOTE]
> [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) 类将忽略 `EnterActions` 和 `ExitActions` 集合中定义的 [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) 对象。    

可以在触发器中同时提供 `EnterActions` 和 `ExitActions`，以及 `Setter`，但注意，将立即调用 `Setter`（它们不等待 `EnterAction` 或 `ExitAction` 完成）  。 或者，可以在代码中执行所有内容，根本无需使用 `Setter`。

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
            <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

如往常一样，如果在 XAML 中引用某个类，应声明命名空间（如 `xmlns:local`），如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

`FadeTriggerAction` 代码如下所示：

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public int StartsFrom { set; get; }

    protected override void Invoke(VisualElement sender)
    {
        sender.Animate("FadeTriggerAction", new Animation((d) =>
        {
            var val = StartsFrom == 1 ? d : 1 - d;
            // so i was aiming for a different color, but then i liked the pink :)
            sender.BackgroundColor = Color.FromRgb(1, val, 1);
        }),
        length: 1000, // milliseconds
        easing: Easing.Linear);
    }
}
```

## <a name="state-triggers"></a>状态触发器

状态触发器已在 Xamarin.Forms 4.5 中引入，这是一组专门的触发器，定义了在哪些条件下应该应用 [`VisualState`](xref:Xamarin.Forms.VisualState)。 不过，它们目前还处于试验阶段，只能通过在 App.xaml.cs  文件中添加以下代码行来使用：

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

状态触发器添加到 [`VisualState`](xref:Xamarin.Forms.VisualState) 的 [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) 集合。 此集合可以包含一个或多个状态触发器。 当此集合中的任何状态触发器处于活动状态时，便会应用 [`VisualState`](xref:Xamarin.Forms.VisualState)。

使用状态触发器来控制视觉对象状态时，Xamarin.Forms 使用以下优先规则来确定哪个触发器（以及相应的 [`VisualState`](xref:Xamarin.Forms.VisualState)）处于活动状态：

1. 任何派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 的触发器。
1. 因满足 [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) 条件而激活的 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)。
1. 因满足 [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) 条件而激活的 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)。

如果多个触发器同时处于活动状态（例如，两个自定义触发器），则标记中声明的第一个触发器优先。

> [!NOTE]
> 状态触发器可以在 [`Style`](xref:Xamarin.Forms.Style) 中设置，也可以直接对元素设置。

若要详细了解视觉对象状态，请参阅 [Xamarin.Forms 视觉对象状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

### <a name="state-trigger"></a>状态触发器

[`StateTrigger`](xref:Xamarin.Forms.StateTrigger) 类派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，包含 [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) 可绑定属性。 当 `IsActive` 属性值更改时，`StateTrigger` 触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。

[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类是所有状态触发器的基类，包含 [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) 属性和 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。 只要 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改，就会触发此事件。

> [!IMPORTANT]
> [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) 可绑定属性隐藏继承的 [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) 属性。

下面的 XAML 示例展示了包含 [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) 对象的 [`Style`](xref:Xamarin.Forms.Style)：

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled}"
                                      IsActiveChanged="OnCheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled, Converter={StaticResource inverseBooleanConverter}}"
                                      IsActiveChanged="OnUncheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此示例中，隐式 [`Style`](xref:Xamarin.Forms.Style) 以 [`Grid`](xref:Xamarin.Forms.Grid) 对象为目标。 当绑定对象的 `IsToggled` 属性为 `true` 时，`Grid` 的背景色设置为黑色。 当绑定对象的 `IsToggled` 属性变成 `false` 时，[`VisualState`](xref:Xamarin.Forms.VisualState) 更改触发，且 `Grid` 的背景色变成白色。

此外，只要 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改，就会触发 `VisualState` 的 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。 每个 `VisualState` 都注册此事件的事件处理程序：

```csharp
void OnCheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Checked state active: {stateTrigger.IsActive}");
}

void OnUncheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Unchecked state active: {stateTrigger.IsActive}");
}
```

在此示例中，当 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件的处理程序触发时，处理程序输出消息来指明 [`VisualState`](xref:Xamarin.Forms.VisualState) 是否处于活动状态。 例如，当视觉对象状态从 `Checked` 更改为 `Unchecked` 时，以下消息输出到控制台窗口中：

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> 自定义状态触发器可以通过派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类来创建。

### <a name="adaptive-trigger"></a>自适应触发器

当窗口为指定高度或宽度时，[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) 触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。 此触发器有以下两个可绑定属性：

- 类型为 `double` 的 [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)，指明触发应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的最小窗口高度。
- 类型为 `double` 的 [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)，指明触发应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的最小窗口宽度。

> [!NOTE]
> 由于 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) 派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，因此可以将事件处理程序附加到 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下面的 XAML 示例展示了包含 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) 对象的 [`Style`](xref:Xamarin.Forms.Style)：

```xaml
<Style TargetType="StackLayout">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Vertical">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="0" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Horizontal">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="800" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此示例中，隐式 [`Style`](xref:Xamarin.Forms.Style) 以 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 对象为目标。 当窗口宽度介于 0 到 800 个设备无关单位之间时，应用 `Style` 的 `StackLayout` 对象采用垂直方向。 当窗口宽度不小于 800 个设备无关单位时，[`VisualState`](xref:Xamarin.Forms.VisualState) 更改触发，且 `StackLayout` 改为采用水平方向：

![垂直 StackLayout VisualState](triggers-images/adaptivetrigger-vertical.png "AdaptiveTrigger 示例")
![水平 StackLayout VisualState](triggers-images/adaptivetrigger-horizontal.png "AdaptiveTrigger 示例")

[`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) 和 [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) 属性可以单独使用，也可以结合使用。 下面的 XAML 示例展示了如何设置这两个属性：

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

在此示例中，[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) 指明在当前窗口宽度不小于 800 个设备无关单位，且当前窗口高度不小于 1200 个设备无关单位时，应用相应的 [`VisualState`](xref:Xamarin.Forms.VisualState)。

### <a name="compare-state-trigger"></a>比较状态触发器

当属性等于特定值时，[`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) 触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。 此触发器有以下两个可绑定属性：

- 类型为 `object` 的 [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property)，指明触发器所比较的属性。
- 类型为 `object` 的 [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value)，指明触发应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的值。

> [!NOTE]
> 由于 [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) 派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，因此可以将事件处理程序附加到 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下面的 XAML 示例展示了包含 [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) 对象的 [`Style`](xref:Xamarin.Forms.Style)：

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="True" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="False" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
...
<Grid>
    <Frame BackgroundColor="White"
           CornerRadius="12"
           Margin="24"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <StackLayout Orientation="Horizontal">
            <CheckBox x:Name="checkBox"
                      VerticalOptions="Center" />
            <Label Text="Check the CheckBox to modify the Grid background color."
                   VerticalOptions="Center" />
        </StackLayout>
    </Frame>
</Grid>
```

在此示例中，隐式 [`Style`](xref:Xamarin.Forms.Style) 以 [`Grid`](xref:Xamarin.Forms.Grid) 对象为目标。 当 [`CheckBox`](xref:Xamarin.Forms.CheckBox) 的 [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) 属性为 `false` 时，`Grid` 的背景色设置为白色。 当 `CheckBox.IsChecked` 属性变成 `true` 时，[`VisualState`](xref:Xamarin.Forms.VisualState) 更改触发，且 `Grid` 的背景色变成黑色：

[![iOS 和 Android 上触发的视觉对象状态更改的屏幕截图](triggers-images/comparestatetrigger-unchecked.png "CompareStateTrigger 示例")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "CompareStateTrigger 示例")
[![iOS 和 Android 上触发的视觉对象状态更改的屏幕截图](triggers-images/comparestatetrigger-checked.png "CompareStateTrigger 示例")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "CompareStateTrigger 示例")

### <a name="device-state-trigger"></a>设备状态触发器

[`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) 根据应用运行所在的设备平台触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。 此触发器有以下一个可绑定属性：

- 类型为 `string` 的 [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device)，指明触发应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的设备平台。

> [!NOTE]
> 由于 [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) 派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，因此可以将事件处理程序附加到 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下面的 XAML 示例展示了包含 `DeviceStateTrigger` 对象的 [`Style`](xref:Xamarin.Forms.Style)：

```xaml
<Style x:Key="DeviceStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="iOS">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="iOS" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Android">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="Android" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="#2196F3" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="UWP">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="UWP" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Aquamarine" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此示例中，显式 [`Style`](xref:Xamarin.Forms.Style) 以 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象为目标。 `ContentPage` 对象使用的 style 将 iOS 上的背景色设置为银色，将 Android 上的背景色设置为淡蓝色，将 UWP 上的背景色设置为海蓝色。 下面的屏幕截图展示了 iOS 和 Android 上的页面效果：

[![iOS 和 Android 上触发的视觉对象状态更改的屏幕截图](triggers-images/devicestatetrigger.png "DeviceStateTrigger 示例")](triggers-images/devicestatetrigger-large.png#lightbox "DeviceStateTrigger 示例")

### <a name="orientation-state-trigger"></a>方向状态触发器

当设备的方向更改时，[`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) 触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。 此触发器有以下一个可绑定属性：

- 类型为 [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation) 的 [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation)，指明触发应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的方向。

> [!NOTE]
> 由于 [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) 派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，因此可以将事件处理程序附加到 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下面的 XAML 示例展示了包含 `OrientationStateTrigger` 对象的 [`Style`](xref:Xamarin.Forms.Style)：

```xaml
<Style x:Key="OrientationStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Portrait">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Portrait" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Landscape">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Landscape" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此示例中，显式 [`Style`](xref:Xamarin.Forms.Style) 以 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象为目标。 `ContentPage` 对象使用的 style 在方向为垂直时将背景色设置为银色，在方向为水平时将背景色设置为白色。

## <a name="related-links"></a>相关链接

- [触发器示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.Forms 视觉对象状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.Forms 触发器 API](xref:Xamarin.Forms.TriggerAction`1)
