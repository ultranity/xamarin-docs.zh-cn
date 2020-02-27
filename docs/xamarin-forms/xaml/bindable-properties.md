---
title: Xamarin。可绑定窗体属性
description: 本文介绍了可绑定属性，并演示如何创建并使用它们。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 56583aa0df676ae55e1b283f1a8e151b69a13d28
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635746"
---
# <a name="xamarinforms-bindable-properties"></a>Xamarin。可绑定窗体属性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

可绑定属性通过使用[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)类型的属性来扩展 CLR 属性功能，而不是使用字段来支持属性。 可绑定属性的用途是提供支持数据绑定、 样式、 模板的属性系统，并通过父-子关系的值设置。 此外，可绑定属性可以提供默认值，验证属性值和监视属性更改回调。

属性应作为可绑定属性，以支持一个或多个以下功能：

- 用作数据绑定的有效*目标*属性。
- 通过[样式](~/xamarin-forms/user-interface/styles/index.md)设置属性。
- 提供默认属性值不同于属性的类型的默认值。
- 正在验证属性的值。
- 监视属性更改。

Xamarin 的示例可绑定的属性包括[`Label.Text`](xref:Xamarin.Forms.Label.Text)、 [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)和[`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)。 每个可绑定属性都具有在同一类上公开的类型[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的相应 `public static readonly` 属性，该属性是可绑定属性的标识符。 例如， [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty)`Label.Text` 属性对应的可绑定属性标识符。

## <a name="create-a-bindable-property"></a>创建可绑定属性

创建可绑定属性的过程如下所示：

1. 使用[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*)方法重载之一创建[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例。
1. 定义[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例的属性访问器。

所有[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例都必须在 UI 线程上创建。 这意味着在 UI 线程运行的代码可以获取或设置可绑定属性的值。 但是，通过使用[`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法封送到 UI 线程，可以从其他线程访问 `BindableProperty` 的实例。

### <a name="create-a-property"></a>创建属性

若要创建 `BindableProperty` 实例，包含类必须从[`BindableObject`](xref:Xamarin.Forms.BindableObject)类派生。 但是，类层次结构中的 `BindableObject` 类很高，因此，用于用户界面功能的大多数类都支持可绑定属性。

可以通过声明[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)类型的 `public static readonly` 属性来创建可绑定的属性。 可绑定的属性应设置为[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法重载之一的返回值。 声明应位于[`BindableObject`](xref:Xamarin.Forms.BindableObject)派生类的正文中，但在任何成员定义的外部。

创建[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)时，必须至少指定标识符，同时指定以下参数：

- [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的名称。
- 属性类型。
- 所属对象的类型。
- 属性的默认值。 这可确保该属性总是返回特定的默认值，未设置，并且它可以不同于属性的类型的默认值。 在可绑定属性上调用[`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty))方法时，将还原默认值。

下面的代码演示具有标识符和四个必需参数的值的可绑定属性的示例：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

这将创建一个类型为 `string`的名为 `EventName`的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例。 属性由 `EventToCommandBehavior` 类拥有，其默认值为 `null`。 可绑定属性的命名约定是可绑定的属性标识符必须与 `Create` 方法中指定的属性名称相匹配，并追加 "Property"。 因此，在上面的示例中，可绑定的属性标识符 `EventNameProperty`。

或者，在创建[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例时，可以指定以下参数：

- 绑定模式。 这用于指定将属性值更改将传播的方向。 在默认绑定模式下，更改*将从源传播到* *目标*。
- 设置属性值时，将调用一个验证委托。 有关详细信息，请参阅[验证回调](#validation-callbacks)。
- 属性更改将属性值已更改时调用的委托。 有关详细信息，请参阅[检测属性更改](#detect-property-changes)。
- 一个属性，更改将属性值将更改时调用的委托。 此委托具有与属性更改委托相同的签名。
- 属性值已更改时，将调用一个强制值委托。 有关详细信息，请参阅[强制值回调](#coerce-value-callbacks)。
- 用于初始化默认属性值的 `Func`。 有关详细信息，请参阅[使用 Func 创建默认值](#create-a-default-value-with-a-func)。

### <a name="create-accessors"></a>创建访问器

属性访问器需要使用属性语法来访问可绑定的属性。 `Get` 访问器应返回相应的可绑定属性中包含的值。 这可以通过以下方式实现：调用[`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，传递要获取该值的可绑定属性标识符，然后将结果转换为所需类型。 `Set` 访问器应设置相应的可绑定属性的值。 这可以通过调用[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法来实现，传递要设置值的可绑定属性标识符，以及要设置的值。

下面的代码示例演示 `EventName` 可绑定属性的访问器：

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>使用可绑定属性

一旦创建可绑定属性后，可以从 XAML 或代码使用它。 在 XAML，这被通过使用命名空间声明，该值指示 CLR 命名空间名称和 （可选） 程序集名称声明具有前缀的命名空间。 有关详细信息，请参阅[XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示了包含所引用的自定义类型的应用程序代码在同一程序集中定义的可绑定属性的自定义类型的 XAML 命名空间：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

设置 `EventName` 可绑定属性时，将使用命名空间声明，如下面的 XAML 代码示例所示：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

以下代码示例显示相应的 C# 代码：

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>高级方案

创建[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)实例时，有许多可选参数可设置为启用高级可绑定属性方案。 本部分探讨这些方案。

### <a name="detect-property-changes"></a>检测属性更改

通过指定[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法的 `propertyChanged` 参数，可以将 `static` 属性更改的回调方法注册到可绑定属性。 可绑定属性的值更改时，将调用指定的回调方法。

下面的代码示例演示 `EventName` 可绑定属性如何将 `OnEventNameChanged` 方法注册为属性更改的回调方法：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

在属性更改的回调方法中， [`BindableObject`](xref:Xamarin.Forms.BindableObject)参数用于表示拥有的类的哪个实例报告了更改，并且两个 `object` 参数的值表示可绑定属性的旧值和新值。

### <a name="validation-callbacks"></a>验证回调

可以通过指定[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法的 `validateValue` 参数，向可绑定的属性注册 `static` 验证回调方法。 设置可绑定属性的值时，将调用指定的回调方法。

下面的代码示例演示 `Angle` 可绑定属性如何将 `IsValidValue` 方法注册为验证回叫方法：

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

验证回调随值提供，如果值对属性有效，则应返回 `true`; 否则 `false`。 如果验证回调返回 `false`（应由开发人员处理），则将引发异常。 可绑定属性设置时，验证回叫方法的典型用法约束整数或双精度型的值。 例如，`IsValidValue` 方法会检查属性值是否为介于0到360范围内的 `double`。

### <a name="coerce-value-callbacks"></a>强制值回调

可以通过指定[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法的 `coerceValue` 参数，向可绑定属性注册 `static` 强制值回叫方法。 可绑定属性的值更改时，将调用指定的回调方法。

> [!IMPORTANT]
> `BindableObject` 类型具有一个 `CoerceValue` 方法，该方法可通过调用其强制值回叫来强制对其 `BindableProperty` 参数的值进行重新计算。

强制的值回叫用于强制重新计算的可绑定属性的属性的值更改时。 例如，可以使用强制值回叫，以确保一个可绑定属性的值不大于另一个可绑定属性的值。

下面的代码示例演示 `Angle` 可绑定属性如何将 `CoerceAngle` 方法注册为强制值回叫方法：

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0, propertyChanged: ForceCoerceValue);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}

static void ForceCoerceValue(BindableObject bindable, object oldValue, object newValue)
{
  bindable.CoerceValue(AngleProperty);
}
```

`CoerceAngle` 方法检查 `MaximumAngle` 属性的值，如果 `Angle` 属性值大于该值，则它会将值强制为 `MaximumAngle` 属性值。 此外，当 `MaximumAngle` 属性更改时，通过调用 `CoerceValue` 方法对 `Angle` 属性调用强制值回调。

### <a name="create-a-default-value-with-a-func"></a>使用 Func 创建默认值

`Func` 可用于初始化可绑定属性的默认值，如下面的代码示例所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator` 参数设置为一个 `Func`，该方法调用[`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法以返回一个表示字体的命名大小的 `double`，该字体在本机平台上的[`Label`](xref:Xamarin.Forms.Label)上使用。

## <a name="related-links"></a>相关链接

- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [事件到命令行为（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [验证回调（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [强制值回叫（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty API](xref:Xamarin.Forms.BindableProperty)
- [Msds-bindableobject API](xref:Xamarin.Forms.BindableObject)
