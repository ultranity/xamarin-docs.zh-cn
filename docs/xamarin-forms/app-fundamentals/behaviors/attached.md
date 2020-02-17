---
title: 附加行为
description: 附加行为是具有一个或多个附加属性的静态类。 本文演示如何创建和使用附加行为。
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7d16eef4fe5422fb5cf3c039c66d1b0f113727fd
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131121"
---
# <a name="attached-behaviors"></a>附加行为

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

“附加行为是具有一个或多个附加属性的静态类。本文演示如何创建和使用附加行为。”

## <a name="overview"></a>概述

附加属性是特殊的可绑定属性。 附加属性在一个类中定义，但可附加到其他对象，并且在 XAML 中可识别为包含类且包含以句点分隔的属性名的属性。

附加属性可以定义 `propertyChanged` 委托，附加属性的值发生变化（例如在控件上设置附加属性）时，将执行该委托。 执行 `propertyChanged` 委托时，它将传递对其所附加的控件的引用，以及包含该属性新旧值的参数。 此委托可用于通过操作传入的引用向该属性所附加的控件添加新功能，如下所示：

1. `propertyChanged` 委托将控件引用（接收为 [`BindableObject`](xref:Xamarin.Forms.BindableObject)）强制转换为旨在增强行为的控制类型。
1. `propertyChanged` 委托修改控件属性、调用控件方法，或为控件公开的事件注册事件处理程序，以实现核心行为功能。

附加行为存在一个问题，即它们使用 `static` 属性和方法在 `static` 类中进行定义。 这使得创建具有状态的附加行为变得困难。 此外，Xamarin.Forms 行为已替代附加行为，成为了行为构建的首选方法。 有关 Xamarin.Forms 行为的更多信息，请参阅 [Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重用行为](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>创建附加行为

示例应用程序演示一个 `NumericValidationBehavior`，并用红色突出显示用户输入到 [`Entry`](xref:Xamarin.Forms.Entry) 控件的值（如果该值不是 `double`）。 此行为如下面的代码示例所示：

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior` 类包含带有 `static` getter 和 setter 且名为 `AttachBehavior` 的附加属性，该属性控制将附加行为的控件添加和删除行为。 该附加属性注册属性值更改时执行的 `OnAttachBehaviorChanged` 方法。 此方法根据 `AttachBehavior` 附加属性的值为 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件注册或注销事件处理程序。 该行为的核心功能由 `OnEntryTextChanged` 方法提供，该方法解析用户输入到 [`Entry`](xref:Xamarin.Forms.Entry) 中的值，如果该值不是 `double`，则将 `TextColor` 属性设置为红色。

## <a name="consuming-an-attached-behavior"></a>使用附加行为

可通过向 [`Entry`](xref:Xamarin.Forms.Entry) 控件添加 `AttachBehavior` 附加属性来使用 `NumericValidationBehavior` 类，如下面的 XAML 代码示例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

下面的代码示例介绍了 C# 中的等效 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在运行时，根据行为实现，行为将响应与控件的交互。 以下屏幕截图演示了响应无效输入的附加行为：

[![](attached-images/screenshots-sml.png "Sample Application with Attached Behavior")](attached-images/screenshots.png#lightbox "Sample Application with Attached Behavior")

> [!NOTE]
> 附加行为是为特定的控件类型（或者可以应用于许多控件的超类）编写的，它们只应添加到可兼容的控件中。 试图将行为附加到不可兼容的控件将导致未知行为，且这取决于行为实现。

### <a name="removing-an-attached-behavior-from-a-control"></a>从控件中删除附加行为

通过将 `AttachBehavior` 附加属性设置为 `false`，可以从控件中删除 `NumericValidationBehavior` 类，如下面的 XAML 代码示例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

下面的代码示例介绍了 C# 中的等效 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在运行时，如果 `AttachBehavior` 附加属性的值设置为 `false` 时，则将执行 `OnAttachBehaviorChanged` 方法。 然后，`OnAttachBehaviorChanged` 方法将注销 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件的事件处理程序，确保用户与控件交互时不执行该行为。

## <a name="summary"></a>总结

本文演示如何创建和使用附加行为。 附加行为是具有一个或多个附加属性的 `static` 类。

## <a name="related-links"></a>相关链接

- [附加行为（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)
