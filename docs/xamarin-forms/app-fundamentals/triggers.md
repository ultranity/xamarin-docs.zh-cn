---
title: Xamarin.Forms 触发器
description: 此文章介绍了如何使用 Xamarin.Forms 触发器来响应 XAML 的用户界面更改。 触发器允许你在根据事件或属性更改更改控件外观的 XAML 中以声明的方式表达操作。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: d046962bf08b85069b1a698324db76a4ac3286d9
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77144646"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 触发器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

触发器允许你在根据事件或属性更改更改控件外观的 XAML 中以声明的方式表达操作。

可以直接分配控件触发器，或将其添加到页面级别或应用级别的资源词典中，以应用到多个控件。

有多种类型的触发器：

- [属性触发器](#property) - 将控件上的属性设置为特定值时发生。

- [数据触发器](#data) - 根据其他控件的属性使用到触发器的数据绑定。

- [事件触发器](#event) - 当控件上发生某事件时发生。

- [多触发器](#multi) - 允许操作发生前设置多个触发条件。

- [自适应触发器](#adaptive)（预览版）- 对应用程序窗口宽度和长度的变化做出回应。

- [比较触发器](#compare)（预览）- 在比较两个值时发生。

- [设备触发器](#device)（预览版）- 在指定的设备上运行时发生。 

- [方向触发器](#orientation)（预览版）- 当设备方向改变时发生。

要使用预览版触发器，请确保使用 `App.xaml.cs` 中的功能标志启用它们：

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

<a name="property" />

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

- **EnterActions 和 ExitActions** （未显示） - 编写于代码中，且可用于 `Setter` 之外（或者不是该元素）的元素。 它们[如下所述](#enterexit)。

### <a name="applying-a-trigger-using-a-style"></a>使用样式应用触发器

还可将触发器添加到控件、页面或应用程序 `ResourceDictionary` 中的 `Style` 声明。 此示例声明隐式样式（即未设置 `Key`），这表示它适用于页面上的所有 `Entry` 控件。

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

<a name="data" />

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

除了指定 `Setter`，你还可提供 [`EnterActions` 和 `ExitActions`](#enterexit)。

<a name="event" />

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

注意：事件触发器不支持[如下所述](#enterexit)的 `EnterActions` 和 `ExitActions`。

<a name="multi" />

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

这些屏幕截图显示了上述两个多触发器示例之间的差异。 在屏幕的顶部，仅一个 `Entry` 中的文本输入便足以启用“保存”按钮。
在屏幕的底部，“登录”按钮在两个字段均包含数据迁保持非活动状态。

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

发生触发器时实现更改的另一方式是通过添加 `EnterActions` 和 `ExitActions` 集合，并指定 `TriggerAction<T>` 实现。

[`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) 集合用于定义将在满足触发条件时调用的 [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) 对象的 `IList`。 [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) 集合用于定义将在不再满足触发条件后调用的 `TriggerAction` 对象的 `IList`。

> [!NOTE]
> [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) 类将忽略 `EnterActions` 和 `ExitActions` 集合中定义的 [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) 对象。    

可以在触发器中同时提供 `EnterActions` 和 `ExitActions`，以及 `Setter`，但注意，将立即调用 `Setter`（它们不等待 `EnterAction` 或 `ExitAction` 完成）。 或者，可以在代码中执行所有内容，根本无需使用 `Setter`。

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

<a name="adaptive" />

## <a name="adaptive-trigger-preview"></a>自适应触发器（预览版）

当窗口为指定的高度或宽度时，`AdaptiveTrigger` 自动触发。 `AdaptiveTrigger` 采用下面两个可能的属性：

- **MinWindowHeight**
- **MinWindowWidth**

<a name="compare"/>

## <a name="compare-trigger-preview"></a>比较触发器（预览版）

`CompareStateTrigger` 是一种超级通用的 `StateTrigger`，它在值与属性相等时触发。

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="Checked">
                <VisualState.StateTriggers>
                    <CompareStateTrigger Property="{Binding IsChecked, Source={x:Reference CheckBox}}" Value="True" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="UnChecked">
                <VisualState.StateTriggers>
                    <CompareStateTrigger Property="{Binding IsChecked, Source={x:Reference CheckBox}}" Value="False" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>     
    </VisualStateManager.VisualStateGroups>  
    <Frame
        HorizontalOptions="Center"
        VerticalOptions="Center"
        BackgroundColor="White"
        Margin="24">
        <StackLayout
            Orientation="Horizontal">
            <CheckBox 
                x:Name="CheckBox"
                VerticalOptions="Center"/>
            <Label
                Text="Checked/Uncheck the CheckBox to modify the Grid BackgroundColor"
                VerticalOptions="Center"/>
        </StackLayout>
    </Frame>
</Grid>
```

此示例展示了如何根据 CheckBox IsChecked 属性的状态修改网格的 BackgroundColor 。 StateTrigger 支持绑定，使你能够采用多种方式来比较 UI 元素的值和 BindingContext 中的值。

<a name="device" />

## <a name="device-trigger-preview"></a>设备触发器（预览版）

通过 `DeviceTrigger`，可控制在特定设备平台上运行时如何应用状态，这与使用 `OnPlatform` 类似。

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState
                x:Name="Android">
                <VisualState.StateTriggers>
                    <DeviceStateTrigger Device="Android" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Blue" />
                </VisualState.Setters>
            </VisualState>
            <VisualState
                x:Name="iOS">
                <VisualState.StateTriggers>
                    <DeviceStateTrigger Device="iOS" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>  
    </VisualStateManager.VisualStateGroups>  
    <Label
        Text="This page changes the color based on the device where the App is running."
        HorizontalOptions="Center"
        VerticalOptions="Center"/>
</Grid>
```

在上例中，背景色在 Android 设备上为蓝色，在 iOS 上为红色。

<a name="orientation" />

## <a name="orientation-trigger-preview"></a>方向触发器（预览版）

当设备在纵向和横向之间切换时，`OrientationTrigger` 支持更改视图状态。

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState
                x:Name="Landscape">
                <VisualState.StateTriggers>
                    <OrientationStateTrigger Orientation="Landscape" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Blue" />
                </VisualState.Setters>
            </VisualState>
            <VisualState
                x:Name="Portrait">
                <VisualState.StateTriggers>
                    <OrientationStateTrigger Orientation="Portrait" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>  
    <Label
        Text="This Grid changes the color based on the orientation device where the App is running."
        HorizontalOptions="Center"
        VerticalOptions="Center"/>
</Grid>
```

在上例中，当设备横向显示时，背景为蓝色，纵向显示时为红色。

## <a name="related-links"></a>相关链接

- [触发器示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.Forms API 文档](xref:Xamarin.Forms.TriggerAction`1)
