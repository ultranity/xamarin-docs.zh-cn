---
title: 平台特定内容
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用和创建平台细节。
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910553"
---
# <a name="platform-specifics"></a>平台特定内容

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。_

使用特定于平台的通过 XAML，或通过 fluent 代码 API 的过程如下所示：

1. 为[`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration)命名空间添加 `xmlns` 声明或 `using` 指令。
1. 为包含特定于平台的功能的命名空间添加 `xmlns` 声明或 `using` 指令：
    1. 在 iOS 上，这是[`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间。
    1. 在 Android 上，这是[`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间。 对于 Android AppCompat，这是[`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空间。
    1. 在通用 Windows 平台上，这是[`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间。
1. 从 XAML 应用特定于平台的，或使用 `On<T>` Fluent API 的代码。 `T` 的值可以是[`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration)命名空间中的[`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS)、 [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android)或[`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows)类型。

> [!NOTE]
> 请注意，尝试使用特定于平台的是不可用的平台上不会导致错误。 相反，该代码将执行而无需特定于平台的应用。

通过 `On<T>` 熟知的代码 API 使用的平台细节返回[`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2)对象。 这允许多个平台特定信息以与方法级联在同一对象上调用。

有关 Xamarin 提供的平台细节的详细信息，请参阅[IOS 平台说明](~/xamarin-forms/platform/ios/index.md)、 [Android 平台细节](~/xamarin-forms/platform/android/index.md)和[Windows 平台细节](~/xamarin-forms/platform/windows/index.md)。

## <a name="creating-platform-specifics"></a>创建平台细节

供应商可以创建自己的平台说明和效果。 影响提供特定功能，然后通过特定于平台的公开。 结果是通过 XAML，并通过 fluent 代码 API 可以更轻松地使用的效果。

创建平台特定的过程如下所示：

1. 实现的特定功能的影响方式。 有关详细信息，请参阅[创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 创建一个特定于平台的类将公开效果。 有关详细信息，请参阅[创建平台特定的类](#creating-a-platform-specific-class)。
1. 特定于平台的类中实现以允许特定于平台的使用通过 XAML 附加的属性。 有关详细信息，请参阅[添加附加属性](#adding-an-attached-property)。
1. 在特定于平台的类中，实现了扩展方法，以允许特定于平台的使用通过 fluent 代码 API。 有关详细信息，请参阅[添加扩展方法](#adding-extension-methods)。
1. 修改效果实现，以便当在已为的效果，在同一平台上调用特定于平台的效果才适用。 有关详细信息，请参阅[创建效果](#creating-the-effect)。

公开平台特定的效果的结果是，效果可以更轻松地使用通过 XAML 和 fluent 代码 API。

> [!NOTE]
> 它是按设想供应商将使用此技术来创建其自己平台特定信息，以便于用户的消耗。 尽管用户可以选择创建自己的平台特定信息，但应该指出，它需要比创建和使用效果更多代码。

该[示例应用程序](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)演示 `Shadow` 平台特定的，它将阴影添加到[`Label`](xref:Xamarin.Forms.Label)控件所显示的文本：

![](images/screenshots.png "Shadow Platform-Specific")

该[示例应用程序](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)在每个平台上实现特定于平台的 `Shadow`，以便于理解。 但是，除了每个特定于平台的效果实现中，卷影类的实现是为每个平台大致相同。 因此，本指南重点介绍卷影类和关联的影响单一平台的实现。

有关效果的详细信息，请参阅[自定义具有效果的控件](~/xamarin-forms/app-fundamentals/effects/index.md)。

### <a name="creating-a-platform-specific-class"></a>创建平台特定的类

平台特定的作为 `public static` 类创建：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

以下各节讨论 `Shadow` 平台特定的和关联的效果的实现。

#### <a name="adding-an-attached-property"></a>添加附加属性

必须将附加属性添加到 `Shadow` 平台特定的，以允许通过 XAML 使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

`IsShadowed` 附加属性用于将 `MyCompany.LabelShadowEffect` 效果添加到 `Shadow` 类附加到的控件，并将其从中移除。 该附加属性注册属性值更改时执行的 `OnIsShadowedPropertyChanged` 方法。 反过来，此方法会调用 `AttachEffect` 或 `DetachEffect` 方法，以便根据 `IsShadowed` 附加属性的值添加或删除效果。 通过修改控件的[`Effects`](xref:Xamarin.Forms.Element.Effects)集合，可以在控件中添加或删除该效果。

> [!NOTE]
> 请注意，被指定为解析组名称和指定效果实现的唯一标识符的串联的值解析效果。 有关详细信息，请参阅[创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

#### <a name="adding-extension-methods"></a>添加扩展方法

扩展方法必须添加到 `Shadow` 平台特定的，以允许通过流畅的代码 API 使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

`IsShadowed` 和 `SetIsShadowed` 扩展方法分别调用 `IsShadowed` 附加属性的 get 和 set 访问器。 每个扩展方法都对 `IPlatformElementConfiguration<iOS, FormsElement>` 类型进行操作，该类型指定平台特定的可从 iOS [`Label`](xref:Xamarin.Forms.Label)实例上调用。

#### <a name="creating-the-effect"></a>创建效果

特定于平台的 `Shadow` 将 `MyCompany.LabelShadowEffect` 添加到[`Label`](xref:Xamarin.Forms.Label)，并将其删除。 以下代码示例展示了 iOS 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`UpdateShadow` 方法将 `Control.Layer` 属性设置为创建阴影，前提是将 `IsShadowed` 附加属性设置为 `true`，并假定已在为其实现该效果的同一平台上调用 `Shadow` 了特定于平台的。 此检查通过 `OnThisPlatform` 方法执行。

如果 `Shadow.IsShadowed` 附加属性值在运行时更改，则此效果需要通过删除阴影来做出响应。 因此，`OnElementPropertyChanged` 方法的重写版本用于通过调用 `UpdateShadow` 方法来响应可绑定的属性更改。

有关创建效果的详细信息，请参阅[创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)和将[效果参数作为附加属性传递](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

### <a name="consuming-the-platform-specific"></a>使用特定于平台的

XAML 中的 `Shadow` 平台特定通过将 `Shadow.IsShadowed` 附加属性设置为 `boolean` 值来使用：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [iOS 平台-详细信息](~/xamarin-forms/platform/ios/index.md)
- [Android 平台-详细信息](~/xamarin-forms/platform/android/index.md)
- [Windows 平台-详细信息](~/xamarin-forms/platform/windows/index.md)
- [自定义具有效果的控件](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [PlatformConfiguration API](xref:Xamarin.Forms.PlatformConfiguration)
