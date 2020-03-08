---
title: 企业应用程序中的验证
description: 本章介绍 eShopOnContainers mobile 应用如何执行用户输入验证。 这包括指定验证规则、触发验证和显示验证错误。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: de5728710a408b8e0c7c68dc89c7e6484cbcc3ce
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915238"
---
# <a name="validation-in-enterprise-apps"></a>企业应用程序中的验证

任何接受用户输入的应用都应确保输入是有效的。 例如，应用程序可以检查输入中是否仅包含特定范围内的字符、是否为特定长度，或是否匹配特定格式。 如果未进行验证，用户提供的数据可能导致应用故障。 验证可强制实施业务规则，并防止攻击者注入恶意数据。

在 ViewModel （MVVM）模式的上下文中，视图模型或模型通常需要执行数据验证并向视图发出任何验证错误信号，以便用户可以更正这些错误。 EShopOnContainers 移动应用执行视图模型属性的同步客户端验证，通过突出显示包含无效数据的控件并显示通知用户的错误消息，通知用户任何验证错误。数据无效的原因。 图6-1 显示了在 eShopOnContainers 移动应用程序中执行验证所涉及的类。

[![](validation-images/validation.png "Validation classes in the eShopOnContainers mobile app")](validation-images/validation-large.png#lightbox "Validation classes in the eShopOnContainers mobile app")

**图 6-1**： eShopOnContainers 移动应用中的验证类

需要验证的视图模型属性为 `ValidatableObject<T>`类型，并且每个 `ValidatableObject<T>` 实例都具有添加到其 `Validations` 属性的验证规则。 验证是通过调用 `ValidatableObject<T>` 实例的 `Validate` 方法从视图模型调用的，该方法检索验证规则并针对 `ValidatableObject<T>` `Value` 属性执行验证规则。 所有验证错误都放置在 `ValidatableObject<T>` 实例的 `Errors` 属性中，并更新 `ValidatableObject<T>` 实例的 `IsValid` 属性，以指示验证是成功还是失败。

属性更改通知由 `ExtendedBindableObject` 类提供，因此[`Entry`](xref:Xamarin.Forms.Entry)的控件可以绑定到视图模型类中 `ValidatableObject<T>` 实例的 `IsValid` 属性，以通知输入的数据是否有效。

## <a name="specifying-validation-rules"></a>指定验证规则

验证规则是通过创建从 `IValidationRule<T>` 接口派生的类来指定的，如下面的代码示例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

此接口指定验证规则类必须提供用于执行所需验证的 `boolean` `Check` 方法，以及一个 `ValidationMessage` 属性，其值为验证错误消息，如果验证失败，将显示该消息。

下面的代码示例演示 `IsNotNullOrEmptyRule<T>` 验证规则，该规则用于在 eShopOnContainers 移动应用中使用模拟服务时，对用户在 `LoginView` 上输入的用户名和密码进行验证：

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

`Check` 方法返回一个 `boolean`，该值指示值参数是 `null`、为空还是仅由空白字符组成。

下面的代码示例演示了用于验证电子邮件地址的验证规则：

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

`Check` 方法返回一个 `boolean`，指示值参数是否为有效的电子邮件地址。 这是通过在 `Regex` 构造函数中搜索指定的正则表达式模式的第一个匹配项的值参数来实现的。 是否在输入字符串中找到了正则表达式模式可以通过检查 `Match` 对象的 `Success` 属性的值来确定。

> [!NOTE]
> 属性验证有时可以涉及依赖属性。 依赖属性的一个示例是，属性 A 的有效值集取决于在属性 B 中设置的特定值。若要检查属性 A 的值是否为允许的值之一，将需要检索属性 B 的值。此外，当属性 B 的值更改时，需要重新验证属性 A。

## <a name="adding-validation-rules-to-a-property"></a>将验证规则添加到属性

在 eShopOnContainers 移动应用中，需要验证的视图模型属性将声明为类型 `ValidatableObject<T>`，其中 `T` 是要验证的数据的类型。 下面的代码示例显示了两个这样的属性的示例：

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

若要进行验证，必须将验证规则添加到每个 `ValidatableObject<T>` 实例的 `Validations` 集合中，如下面的代码示例所示：

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

此方法将 `IsNotNullOrEmptyRule<T>` 验证规则添加到每个 `ValidatableObject<T>` 实例的 `Validations` 集合，指定验证规则的 `ValidationMessage` 属性的值，该属性指定验证失败时将显示的验证错误消息。

## <a name="triggering-validation"></a>触发验证

EShopOnContainers 移动应用中使用的验证方法可以手动触发属性验证，并在属性发生更改时自动触发验证。

### <a name="triggering-validation-manually"></a>手动触发验证

可以为视图模型属性手动触发验证。 例如，当用户点击 `LoginView`上的 "**登录**" 按钮时，会在 eShopOnContainers 移动应用中出现这种情况。 命令委托调用 `LoginViewModel`中的 `MockSignInAsync` 方法，该方法通过执行 `Validate` 方法来调用验证，如以下代码示例所示：

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

`Validate` 方法通过对每个 `ValidatableObject<T>` 实例调用 Validate 方法来对 `LoginView`上的用户输入的用户名和密码执行验证。 下面的代码示例演示了 `ValidatableObject<T>` 类中的 Validate 方法：

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

此方法清除 `Errors` 集合，然后检索添加到对象 `Validations` 集合中的任何验证规则。 将执行每个检索到的验证规则的 `Check` 方法，并将无法验证数据的任何验证规则的 `ValidationMessage` 属性值添加到 `ValidatableObject<T>` 实例的 `Errors` 集合。 最后，设置 `IsValid` 属性，并将其值返回给调用方法，指示验证是成功还是失败。

### <a name="triggering-validation-when-properties-change"></a>属性更改时触发验证

只要绑定属性发生更改，也可以触发验证。 例如，当 `LoginView` 中的双向绑定设置 `UserName` 或 `Password` 属性时，将触发验证。 下面的代码示例演示了这种情况的发生方式：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

[`Entry`](xref:Xamarin.Forms.Entry)控件绑定到 `ValidatableObject<T>` 实例的 `UserName.Value` 属性，控件的 `Behaviors` 集合添加了一个 `EventToCommandBehavior` 实例。 此行为会执行 `ValidateUserNameCommand`，以响应在 `Entry`上触发的 [`TextChanged`] 事件，该事件在 `Entry` 中的文本更改时引发。 反过来，`ValidateUserNameCommand` 委托执行 `ValidateUserName` 方法，该方法对 `ValidatableObject<T>` 实例执行 `Validate` 方法。 因此，每次用户在 `Entry` 控件中为用户名输入一个字符时，都会对输入的数据进行验证。

有关行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>显示验证错误

EShopOnContainers 移动应用通过以下方式向用户通知任何验证错误：突出显示包含无效数据的控件，并显示一条错误消息，告知用户数据在包含数据无效。 当更正无效数据时，该行将变为黑色，并删除错误消息。 图6-2 显示验证错误时 eShopOnContainers 移动应用中的登录视图。

![](validation-images/validation-login.png "Displaying validation errors during login")

**图6-2：** 在登录过程中显示验证错误

### <a name="highlighting-a-control-that-contains-invalid-data"></a>突出显示包含无效数据的控件

`LineColorBehavior` 附加行为用于突出显示发生验证错误[`Entry`](xref:Xamarin.Forms.Entry)控件。 下面的代码示例演示如何将 `LineColorBehavior` 附加行为附加到 `Entry` 控件：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

[`Entry`](xref:Xamarin.Forms.Entry)控件使用显式样式，如以下代码示例所示：

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

此样式设置[`Entry`](xref:Xamarin.Forms.Entry)控件上 `LineColorBehavior` 附加行为的 `ApplyLineColor` 和 `LineColor` 附加属性。 有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

如果设置或更改 `ApplyLineColor` 附加属性的值，则 `LineColorBehavior` 附加行为将执行 `OnApplyLineColorChanged` 方法，如以下代码示例所示：

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

此方法的参数提供行为所附加到的控件的实例，以及 `ApplyLineColor` 附加属性的新旧值。 如果 `true``ApplyLineColor` 附加属性，则会将 `EntryLineColorEffect` 类添加到控件的[`Effects`](xref:Xamarin.Forms.Element.Effects)集合，否则将从控件的 `Effects` 集合中将其删除。 有关行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect` 子类[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)类，如下面的代码示例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)类表示一个独立于平台的效果，该效果封装了平台特定的内部效果。 这简化了效果删除过程，因为对于特定于平台的效果，没有对类型信息的编译时访问。 `EntryLineColorEffect` 将调用基类构造函数，并传入一个参数，该参数包含解析组名称的串联，以及在每个特定于平台的效果类上指定的唯一 ID。

下面的代码示例演示适用于 iOS 的 `eShopOnContainers.EntryLineColorEffect` 实现：

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

`OnAttached` 方法检索 Xamarin. Forms [`Entry`](xref:Xamarin.Forms.Entry)控件的本机控件，并通过调用 `UpdateLineColor` 方法更新线条颜色。 如果附加的 `LineColor` 属性发生更改，则 `OnElementPropertyChanged` 重写通过更新 `Entry` 控件上的可绑定属性更改，或 `Entry` 的[`Height`](xref:Xamarin.Forms.VisualElement.Height)属性发生更改。 有关效果的更多信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

当在[`Entry`](xref:Xamarin.Forms.Entry)控件中输入有效数据时，它将在控件的底部应用一条黑线，以指示不存在验证错误。 图6-3 显示了一个示例。

![](validation-images/validation-blackline.png "Black line indicating no validation error")

**图 6-3**：指示无验证错误的黑色线条

[`Entry`](xref:Xamarin.Forms.Entry)控件还向其[`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)集合添加了一个[`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 。 下面的代码示例演示了 `DataTrigger`：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

此[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)会监视 `UserName.IsValid` 属性，如果它的值变为 `false`，它将执行[`Setter`](xref:Xamarin.Forms.Setter)，这会将 `LineColor` 附加行为的附加属性更改为红色。`LineColorBehavior` 图6-4 显示了一个示例。

![](validation-images/validation-redline.png "Red line indicating validation error")

**图 6-4**：指示验证错误的红色行

输入的数据无效时， [`Entry`](xref:Xamarin.Forms.Entry)控件中的行会保持为红色; 否则，它将更改为黑色以指示输入的数据有效。

有关触发器的详细信息，请参阅[触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

### <a name="displaying-error-messages"></a>显示错误消息

UI 在其数据验证失败的每个控件下的标签控件中显示验证错误消息。 下面的代码示例演示了在用户未输入有效用户名的情况下显示验证错误消息的[`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每个[`Label`](xref:Xamarin.Forms.Label)都绑定到要验证的视图模型对象的 `Errors` 属性。 `Errors` 属性由 `ValidatableObject<T>` 类提供，其类型为 `List<string>`。 由于 `Errors` 属性可能包含多个验证错误，因此 `FirstValidationErrorConverter` 实例用于从集合中检索要显示的第一个错误。

## <a name="summary"></a>总结

EShopOnContainers 移动应用执行视图模型属性的同步客户端验证，通过突出显示包含无效数据的控件并显示通知用户的错误消息，通知用户任何验证错误。数据无效的原因。

需要验证的视图模型属性为 `ValidatableObject<T>`类型，并且每个 `ValidatableObject<T>` 实例都具有添加到其 `Validations` 属性的验证规则。 验证是通过调用 `ValidatableObject<T>` 实例的 `Validate` 方法从视图模型调用的，该方法检索验证规则并针对 `ValidatableObject<T>` `Value` 属性执行验证规则。 所有验证错误都放置在 `ValidatableObject<T>`实例的 `Errors` 属性中，并更新 `ValidatableObject<T>` 实例的 `IsValid` 属性，以指示验证是成功还是失败。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
