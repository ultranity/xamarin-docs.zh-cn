---
title: Xamarin 格式视觉对象
description: Xamarin。窗体材料视觉对象可用于创建在 iOS 和 Android 上外观大致相同的 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/25/2019
ms.openlocfilehash: 81ef3c44d6eb8aaf4dd0ec467e11ef04adb02a76
ms.sourcegitcommit: 5a6124271a679b8961fa9430bd738fcb18544e92
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618408"
---
# <a name="xamarinforms-material-visual"></a>Xamarin 格式视觉对象

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[材料设计](https://material.io)是 Google 创建的固执设计系统，它规定了视图和布局的外观和行为方式的大小、颜色、间距和其他方面。

Xamarin。窗体材料视觉对象可用于将材料设计规则应用于 Xamarin。 Forms 应用程序，创建在 iOS 和 Android 上大致完全相同的应用程序。 启用材料视觉对象后，支持的视图将采用相同的设计跨平台，从而形成统一的外观。

[![材料视觉屏幕快照](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

在应用程序中启用 Xamarin 格式视觉对象的过程如下：

1. 将[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包添加到 IOS 和 Android 平台项目。 此 NuGet 包提供 iOS 和 Android 上经过优化的材料设计呈现器。 在 iOS 上，包提供与[MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可传递依赖项，这是与 Google 的C# [ios 材料组件](https://material.io/develop/ios/)的绑定。 在 Android 上，包提供了生成目标，以确保正确设置 TargetFramework。
1. 初始化每个平台项目中的材料视觉对象。 有关详细信息，请参阅[初始化材料视觉对象](#initialize-material-visual)。
1. 通过将[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性设置为应采用材料设计规则的任何页上 `Material` 来创建材料视觉对象。 有关详细信息，请参阅[使用材料呈现](#apply-material-visual)器。
1. 可有可无自定义材料控件。 有关详细信息，请参阅[自定义材料控件](#customize-material-visual)。

> [!IMPORTANT]
> 在 Android 上，材料视觉对象要求最低版本5.0 （API 21）或更高版本，以及版本9.0 （API 28）的 TargetFramework。 此外，你的平台项目需要28.0.0 或更高版本的 Android 支持库，其主题需要从材料组件主题继承或继续从 AppCompat 主题继承。 有关详细信息，请参阅[适用于 Android 的材料组件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)入门。

材料视觉对象目前支持以下控件：

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- [`CheckBox`](xref:Xamarin.Forms.CheckBox)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

材料控件通过材料呈现器来实现，这些呈现器应用材料设计规则。 在功能上，内容呈现器与默认呈现器没有区别。 有关详细信息，请参阅[自定义材料视觉对象](#customize-material-visual)。

## <a name="initialize-material-visual"></a>初始化材料视觉对象

安装[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包后，必须在每个平台项目中初始化材料呈现器。

在 iOS 上，在 `Xamarin.Forms.Forms.Init` 方法*后*调用 `Xamarin.Forms.FormsMaterial.Init` 方法，这应在**AppDelegate.cs**中发生：

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

在 Android 上，在 `Xamarin.Forms.Forms.Init` 方法*后*调用 `Xamarin.Forms.FormsMaterial.Init` 方法，这应在**MainActivity.cs**中发生：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>应用材料视觉对象

应用程序可以通过将页面、布局或视图上的[`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性设置为 `Material`来启用材料视觉对象：

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

等效 C# 代码如下：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

将 `VisualElement.Visual` 属性设置为 "`Material` 指示应用程序使用材料视觉呈现器而不是默认呈现器。 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性可以设置为任何实现 `IVisual`的类型， [`VisualMarker`](xref:Xamarin.Forms.VisualMarker)类提供以下 `IVisual` 属性：

- `Default` –指示视图应使用默认呈现器呈现。
- `MatchParent` –指示视图应使用与它的直接父级相同的呈现器。
- `Material` –指示视图应使用材料呈现器呈现。

> [!IMPORTANT]
> [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性是在[`VisualElement`](xref:Xamarin.Forms.VisualElement)类中定义的，视图从其父项继承 `Visual` 属性值。 因此，设置[`ContentPage`](xref:Xamarin.Forms.ContentPage)上的 `Visual` 属性可以确保页面中任何受支持的视图都将使用该视觉对象。 此外，可以在视图上覆盖 `Visual` 属性。

以下屏幕截图显示了使用默认呈现器呈现的用户界面：

[![IOS 和 Android 上默认呈现器的屏幕截图](material-visual-images/default-renderers.png "使用默认呈现器的视图")](material-visual-images/default-renderers-large.png#lightbox)

以下屏幕截图显示了使用材料呈现器呈现的相同用户界面：

[![IOS 和 Android 上的材料呈现器的屏幕截图](material-visual-images/material-renderers.png "使用材料呈现器的视图")](material-visual-images/material-renderers-large.png#lightbox)

默认呈现器和材料呈现器之间的主要可见差异（如下所示）是材料呈现器是否大写[`Button`](xref:Xamarin.Forms.Button)文本，以及[`Frame`](xref:Xamarin.Forms.Frame)边框的角。 但是，材料呈现器使用本机控件，因此，字体、阴影、颜色和提升等区域的平台之间可能仍然存在用户界面差异。

> [!NOTE]
> 材料设计组件严格遵守 Google 的准则。 因此，材料设计呈现器倾向于调整大小和行为。 如果需要更好地控制样式或行为，则仍可创建自己的[效果](~/xamarin-forms/app-fundamentals/effects/index.md)、[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)或[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器来实现所需的详细信息。

## <a name="customize-material-visual"></a>自定义材料视觉对象

材料 Visual NuGet 包是实现 Xamarin. Forms 控件的呈现器集合。 自定义材料视觉对象与自定义默认控件完全相同。

如果目标是自定义现有控件，则建议使用效果。 如果存在材料可视化呈现器，则使用效果自定义控件的效果要比将呈现器划分为子类更少。 有关效果的详细信息，请参阅[Xamarin。窗体效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

当材料呈现器不存在时，建议使用自定义呈现器。 以下呈现器类包含在材料视觉对象中：

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

对于材料呈现器，与非材料呈现器的子类几乎相同。 但是，当导出子类中的呈现器时，必须为指定 `VisualMarker.MaterialVisual` 类型的 `ExportRenderer` 属性提供第三个参数：

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
    }
}
```

在此示例中，`ExportRendererAttribute` 指定 `CustomMaterialProgressBarRenderer` 类将用于呈现[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)视图，并将 `IVisual` 类型注册为第三个参数。

> [!NOTE]
> 指定 `IVisual` 类型（作为其 `ExportRendererAttribute`的一部分）的呈现器将用于呈现所选择的视图，而不是默认呈现器。 在呈现器选择时间，将检查视图的 `Visual` 属性，并将其包含在呈现器选择过程中。

有关自定义呈现器的详细信息，请参阅[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

## <a name="related-links"></a>相关链接

- [材料视觉对象（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [创建 Xamarin Forms 视觉对象呈现器](create.md)
- [Xamarin. Forms 效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
