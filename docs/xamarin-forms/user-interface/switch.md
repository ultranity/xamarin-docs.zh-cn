---
title: Xamarin Forms 开关
description: Xamarin 开关是一种按钮类型，用户可以对其进行操作以在开启和关闭状态之间切换。 本文介绍如何使用 Switch 类显示切换 UI 元素。
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 88655aabdbd32db63aaf3330a18b0ad8105ea26c
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506534"
---
# <a name="xamarinforms-switch"></a>Xamarin Forms 开关

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Xamarin [`Switch`](xref:Xamarin.Forms.Switch)控件是一个水平切换按钮，用户可以对其进行操作以在打开和关闭状态之间切换，这些状态由 `boolean` 值表示。 `Switch` 类继承自[`View`](xref:Xamarin.Forms.View)。

以下屏幕截图显示了在 iOS 和 Android**上**的**开关状态**下的 `Switch` 控件：

![IOS 和 Android 上的开启和关闭状态的开关屏幕截图](switch-images/switch-states-default.png "IOS 和 Android 上的交换机")

`Switch` 控件定义以下属性：

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)是一个 `boolean` 值，该值指示 `Switch` 是否为**on**。
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)是一种 `Color`，它会影响在**切换或状态下呈现**`Switch` 的方式。
* `ThumbColor` 是交换机拇指的 `Color`。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着可以对 `Switch` 进行样式化，并使其成为数据绑定的目标。

`Switch` 控件定义一个 `Toggled` 事件，该事件在 `IsToggled` 属性发生更改时通过用户操作或在应用程序设置 `IsToggled` 属性时引发。 `Toggled` 事件附带的 `ToggledEventArgs` 对象具有名为 `Value`的单个属性，类型 `bool`。 触发事件时，`Value` 属性的值将反映 `IsToggled` 属性的新值。

## <a name="create-a-switch"></a>创建开关

可以在 XAML 中实例化 `Switch`。 可以设置其 `IsToggled` 属性以切换 `Switch`。 默认情况下，`IsToggled` 属性为 `false`。 下面的示例演示如何使用可选的 `IsToggled` 属性集在 XAML 中实例化 `Switch`：

```xaml
<Switch IsToggled="true"/>
```

还可以在代码中创建 `Switch`：

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>切换外观

除了[`Switch`](xref:Xamarin.Forms.Switch)继承自[`View`](xref:Xamarin.Forms.View)类的属性以外，`Switch` 还定义 `OnColor` 和 `ThumbColor` 属性。 `OnColor` 属性可以设置**为在切换到其状态时**定义 `Switch` 颜色，并且 `ThumbColor` 属性可以设置为定义 switch thumb 的 `Color`。 下面的示例演示如何在 XAML 中使用以下属性集来实例化 `Switch`：

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

在代码中创建 `Switch` 时，还可以设置属性：

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

下面的屏幕截图显示了 "打开" 和 **"** **关闭**" 切换状态下的 `Switch`，并设置了 "`OnColor`" 和 "`ThumbColor`" 属性：

![IOS 和 Android 上的开启和关闭状态的开关屏幕截图](switch-images/switch-states-colors.png "IOS 和 Android 上的交换机")

## <a name="respond-to-a-switch-state-change"></a>响应交换机状态更改

当 `IsToggled` 属性更改时，无论是通过用户操作更改，还是在应用程序设置 `IsToggled` 属性时，将激发 `Toggled` 事件。 可注册此事件的事件处理程序以响应更改：

```xaml
<Switch Toggled="OnToggled" />
```

代码隐藏文件包含 `Toggled` 事件的处理程序：

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

事件处理程序中的 `sender` 参数是负责引发此事件的 `Switch`。 您可以使用 `sender` 属性访问 `Switch` 对象，或区分共享同一 `Toggled` 事件处理程序的多个 `Switch` 对象。

还可以在代码中对 `Toggled` 事件处理程序进行赋值：

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>数据绑定开关

可以通过使用数据绑定和触发器来响应 `Switch` 更改切换状态，从而消除 `Toggled` 事件处理程序。

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label)在 `DataTrigger` 中使用绑定表达式来监视名为 `styleSwitch`的 `Switch` 的 `IsToggled` 属性。 如果此属性 `true`，则会更改 `Label` 的 `FontAttributes` 和 `FontSize` 属性。 当 `IsToggled` 属性返回到 `false`时，`Label` 的 `FontAttributes` 和 `FontSize` 属性将重置为它们的初始状态。

有关触发器的信息，请参阅[Xamarin。窗体触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-switch"></a>禁用交换机

应用程序可能会进入一个状态，在该状态下，要切换的 `Switch` 不是有效操作。 在这种情况下，可以通过将 `Switch` 的 `IsEnabled` 属性设置为 `false`来禁用。 这会阻止用户操作 `Switch`。

## <a name="related-links"></a>相关链接

* [切换演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Xamarin。窗体触发器](~/xamarin-forms/app-fundamentals/triggers.md)
