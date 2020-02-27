---
title: 在 Xamarin.Forms 中的自定义动画
description: 本文演示如何使用 Xamarin.FOrms 动画类来创建和取消动画，同步多个动画，并创建不通过现有的动画方法进行动画处理的属性进行动画处理的自定义动画。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
ms.openlocfilehash: 405d7990b622b890aa3d66bd632662f086441666
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635752"
---
# <a name="custom-animations-in-xamarinforms"></a>在 Xamarin.Forms 中的自定义动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_动画类是所有 Xamarin. Forms 动画的构建基块，其中包含 ViewExtensions 类中的扩展方法，用于创建一个或多个动画对象。本文演示如何使用动画类来创建和取消动画，同步多个动画，以及创建自定义动画，以对未被现有动画方法进行动画处理的属性进行动画处理。_

创建 `Animation` 对象时，必须指定多个参数，包括正在进行动画处理的属性的开始值和结束值，以及更改属性值的回调。 `Animation` 对象还可以维护可运行和同步的子动画集合。 有关详细信息，请参阅[子动画](#child)。

可以通过调用[`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法来运行使用[`Animation`](xref:Xamarin.Forms.Animation)类创建的动画，该动画可能包含也可能不包含子动画。 此方法指定的持续时间的动画，并在其他项目之间的回调，用于控制是否重复动画。

此外， [`Animation`](xref:Xamarin.Forms.Animation)类具有一个 `IsEnabled` 属性，可通过检查该属性来确定操作系统是否已禁用动画，例如当电源节能模式处于激活状态时。

## <a name="create-an-animation"></a>创建动画

创建[`Animation`](xref:Xamarin.Forms.Animation)对象时，通常需要至少三个参数，如以下代码示例所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此代码定义从值1到值2的[`Image`](xref:Xamarin.Forms.Image)实例的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性的动画。 经过动画处理的值（由 Xamarin. Forms 派生）传递给指定为第一个参数的回调，此参数用于更改 `Scale` 属性的值。

动画通过调用[`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法开始，如以下代码示例所示：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

请注意， [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法不会返回 `Task` 对象。 相反，它们通过回调方法提供通知。

以下参数是在 `Commit` 方法中指定的：

- 第一个参数（*所有者*）标识动画的所有者。 这可以是应用动画的可视元素或另一个可视元素，如页。
- 第二个参数（*名称*）用名称标识动画。 名称结合了多个要唯一标识该动画的所有者。 然后，可以使用此唯一标识来确定动画是否正在运行（[`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))）或取消（[`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))）。
- 第三个参数（*rate*）指示每次调用[`Animation`](xref:Xamarin.Forms.Animation)构造函数中定义的回调方法之间的毫秒数
- 第四个参数（*长度*）指示动画的持续时间（以毫秒为单位）。
- 第五个参数（*缓动*）定义要在动画中使用的缓动函数。 或者，可将缓动函数指定为[`Animation`](xref:Xamarin.Forms.Animation)构造函数的参数。 有关缓动函数的详细信息，请参阅[缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。
- 第六个参数（*完成*）是在动画完成后将执行的回调。 此回调采用两个参数，第一个参数指示最终值，第二个参数是设置为 `true` 的 `bool` （如果动画已取消）。 另外，还可以将*已完成*的回调指定为[`Animation`](xref:Xamarin.Forms.Animation)构造函数的参数。 但是，对于单个动画，如果在 `Animation` 构造函数和 `Commit` 方法中同时指定*完成*的回调，则只会执行在 `Commit` 方法中指定的回调。
- 第七个参数（*重复*）是允许重复动画的回调。 它在动画结束时调用，返回 `true` 指示动画应重复。

总体效果是使用[`Linear`](xref:Xamarin.Forms.Easing.Linear)缓动函数创建动画，以将[`Image`](xref:Xamarin.Forms.Image)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性从1增加到2（2000毫秒）以上。 每次动画完成时，其 `Scale` 属性都将重置为1，动画将重复。

> [!NOTE]
> 并发动画可以通过为每个动画创建一个 `Animation` 对象，然后对每个动画调用 `Commit` 方法来构建。

<a name="child" />

### <a name="child-animations"></a>子动画

[`Animation`](xref:Xamarin.Forms.Animation)类还支持子动画，子动画涉及创建向其中添加其他 `Animation` 对象的 `Animation` 对象。 这样，动画将运行和同步的一系列。 下面的代码示例演示如何创建和运行子动画：

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

或者，可以更加简洁，如下面的代码示例所示编写代码示例：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在这两个代码示例中，将创建一个父[`Animation`](xref:Xamarin.Forms.Animation)对象，然后向其中添加其他 `Animation` 对象。 [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))方法的前两个参数指定开始和完成子动画的时间。 参数值必须是 0 和 1 之间，并且表示在父动画指定的子动画将处于活动状态的相对期限。 因此，在此示例中，`scaleUpAnimation` 将在动画的前半部分处于活动状态，`scaleDownAnimation` 在动画的后半部分处于活动状态，`rotateAnimation` 将在整个持续时间内处于活动状态。

总体效果是动画发生超过 4 秒 （4000 毫秒为单位）。 `scaleUpAnimation` 将[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性从1到2的动画处理，超过2秒。 然后，`scaleDownAnimation` 在2秒内对 `Scale` 属性从2到1进行动画处理。 同时出现这两种缩放动画时，`rotateAnimation` 将[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性从0到360，超过4秒。 请注意，缩放动画还使用缓动函数。 [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)缓动函数会使[`Image`](xref:Xamarin.Forms.Image)在获取更大值之前进行收缩，而[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)缓动函数会使 `Image` 小于其在整个动画结束时的实际大小。

使用子动画的[`Animation`](xref:Xamarin.Forms.Animation)对象与不使用子动画的对象之间存在很多差异：

- 使用子动画时，子动画上*完成*的回调将指示子动画完成的时间，并且传递到 `Commit` 方法的*完成*回调将指示整个动画完成的时间。
- 使用子动画时，从 `Commit` 方法上的*重复*回调返回 `true` 将不会导致动画重复，但动画将继续运行，而不会产生新值。
- 在 `Commit` 方法中包含缓动函数，并且缓动函数返回一个大于1的值时，动画将终止。 如果缓动函数返回的值小于 0，其值限制为 0。 若要使用返回小于0或大于1的值的缓动函数，则必须在其中一个子动画（而不是 `Commit` 方法）中指定该值。

[`Animation`](xref:Xamarin.Forms.Animation)类还包括可用于向父 `Animation` 对象添加子动画的[`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))方法。 但是，它们的*开始*和*结束*参数值不会限制为0到1，但只有对应于0到1范围的子动画部分才会处于活动状态。 例如，如果 `WithConcurrent` 方法调用定义以1到6之间的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性为目标的子动画，但其*begin*和*finish*值为-2 和3，则-2 的*开始*值对应于 `Scale` 值1，而*完成*值3对应于 `Scale` 值6。 由于不在0和1范围内的值在动画中的任何部分都不起作用，因此 `Scale` 属性将仅从3到6进行动画处理。

## <a name="cancel-an-animation"></a>取消动画

应用程序可以通过调用[`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))扩展方法来取消动画，如以下代码示例所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

请注意动画进行动画所有者和动画名称的组合唯一标识。 因此，所有者和名称指定何时运行动画必须指定要取消动画。 因此，该代码示例将立即取消页面所拥有的名为 `SimpleAnimation` 的动画。

## <a name="create-a-custom-animation"></a>创建自定义动画

到目前为止，这里显示的示例演示了使用[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类中的方法可以同样实现的动画。 但[`Animation`](xref:Xamarin.Forms.Animation)类的优点是它有权访问回调方法，该方法是在动画值更改时执行的。 这样，要实现所需的任何动画的回调。 例如，下面的代码示例通过将页的[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)属性设置为[`Color`](xref:Xamarin.Forms.Color)由[`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))方法创建的值（其色相值介于0和1之间）对其进行动画处理：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

生成的动画提供了提前出喷薄彩虹的颜色通过页面背景的外观。

有关创建复杂动画的更多示例，包括贝塞尔曲线动画，请参阅[使用 Xamarin 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)的第[22 章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)。

## <a name="create-a-custom-animation-extension-method"></a>创建自定义动画扩展方法

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类中的扩展方法将属性从其当前值动画处理为指定值。 例如，这样做很难创建一个 `ColorTo` 动画方法，该方法可用于从一个值向另一个值对颜色进行动画处理，因为：

- [`VisualElement`](xref:Xamarin.Forms.VisualElement)类定义的唯一[`Color`](xref:Xamarin.Forms.Color)属性是[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)，这并不总是要进行动画处理的所需 `Color` 属性。
- 通常， [`Color`](xref:Xamarin.Forms.Color)属性的当前值是[`Color.Default`](xref:Xamarin.Forms.Color.Default)，这不是实际颜色，不能在内插计算中使用。

此问题的解决方法是不让 `ColorTo` 方法以特定[`Color`](xref:Xamarin.Forms.Color)属性为目标。 相反，可以使用回调方法编写该方法，该回调方法将内插 `Color` 值传递回调用方。 此外，该方法将采用 `Color` 参数的开头和结尾。

`ColorTo` 方法可以实现为扩展方法，该方法使用[`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions)类中的[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法来提供其功能。 这是因为 `Animate` 方法可用于以不属于类型 `double`的属性为目标，如下面的代码示例所示：

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法需要一个*转换*参数，该参数是一个回调方法。 此回调的输入始终为介于0到1之间的 `double`。 因此，`ColorTo` 方法定义了其自己的转换 `Func`，该转换接受范围为0到1的 `double`，并返回对应于该值的[`Color`](xref:Xamarin.Forms.Color)值。 `Color` 值是通过将两个提供的`A`参数的[`R`](xref:Xamarin.Forms.Color.R)、 [`G`](xref:Xamarin.Forms.Color.G)、 [`B`](xref:Xamarin.Forms.Color.B)和[`Color`](xref:Xamarin.Forms.Color.A)值插值计算得出的。 然后，`Color` 值将传递给特定属性的应用程序的回调方法。

此方法允许 `ColorTo` 方法对任何[`Color`](xref:Xamarin.Forms.Color)属性进行动画处理，如下面的代码示例所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此代码示例中，`ColorTo` 方法对[`Label`](xref:Xamarin.Forms.Label)的[`TextColor`](xref:Xamarin.Forms.Label.TextColor)和[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)属性、页面的 `BackgroundColor` 属性和[`Color`](xref:Xamarin.Forms.BoxView)的[`BoxView`](xref:Xamarin.Forms.BoxView.Color)属性进行动画处理。

## <a name="related-links"></a>相关链接

- [自定义动画（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [动画 API](xref:Xamarin.Forms.Animation)
- [AnimationExtensions API](xref:Xamarin.Forms.AnimationExtensions)
