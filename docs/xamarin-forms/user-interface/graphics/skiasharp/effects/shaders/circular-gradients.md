---
title: SkiaSharp 循环渐变
description: 了解有关不同类型的基于圆圈的渐变。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916388"
---
# <a name="the-skiasharp-circular-gradients"></a>SkiaSharp 循环渐变

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKShader`](xref:SkiaSharp.SKShader)类定义静态方法，以创建四种不同类型的渐变。 [**SkiaSharp 线性渐变**](linear-gradient.md)文章讨论[`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*)方法。 本文介绍了其他三种类型的渐变，所有这些都按圆圈。

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*)方法创建从圆的中心 emanates 的渐变：

![径向渐变示例](circular-gradients-images/RadialGradientSample.png)

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*)方法创建一个围绕圆圈中心进行扫描的渐变：

![扫描渐变示例](circular-gradients-images/SweepGradientSample.png)

第三个渐变的类型是非常不寻常。 它被称为双点圆锥渐变，由[`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*)方法定义。 渐变到另一个扩展从一个圆：

![圆锥渐变示例](circular-gradients-images/ConicalGradientSample.png)

如果两个圆是不同的大小，渐变采用圆锥体的形式。

本文探讨了这些渐变中更多详细信息。

## <a name="the-radial-gradient"></a>径向渐变

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有以下语法：

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载还包含转换矩阵参数。

前两个参数指定的中心圆和半径。 渐变从该中心开始，并向外扩展 `radius` 像素。 `radius` 依赖于[`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode)参数会发生什么情况。 `colors` 参数是两种或多种颜色的数组（与线性渐变方法相同），`colorPos` 是介于0到1之间的整数数组。 这些整数表示颜色沿 `radius` 线条的相对位置。 您可以将该参数设置为 `null`，以便在相同的颜色上设置大小。

如果使用 `CreateRadialGradient` 填充圆圈，则可以将渐变中心设置为圆的中心，将渐变的半径设置为圆的半径。 在这种情况下，`SKShaderTileMode` 参数对渐变的呈现不起作用。 但如果渐变填充的区域大于渐变定义的圆，则 `SKShaderTileMode` 参数对圆外发生的情况有深远的影响。

`SKShaderMode` 的效果在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中的 "**径向渐变**" 页中进行演示。 此页的 XAML 文件将实例化一个 `Picker`，该允许您选择 `SKShaderTileMode` 枚举的三个成员之一：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

代码隐藏文件的颜色使用径向渐变整个画布。 渐变中心设置为在画布的中心和半径设置为 100 像素。 只需两种颜色，黑色或白色包括渐变：

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

此代码创建具有中心，逐渐淡出为白色 100 像素从中心的黑色的渐变。 超出 radius 的情况取决于 `SKShaderTileMode` 参数：

[![径向渐变](circular-gradients-images/RadialGradient.png "径向渐变")](circular-gradients-images/RadialGradient-Large.png#lightbox)

在所有三种情况下，渐变填充画布。 在左侧的 iOS 屏幕上超出半径渐变继续执行上一种颜色为白色。 这就是 `SKShaderTileMode.Clamp`的结果。 Android 屏幕显示 `SKShaderTileMode.Repeat`的效果：从中心开始100像素，渐变将再次开始，第一种颜色为黑色。 渐变重复 radius 每 100 个像素。 

右侧的 "通用 Windows 平台" 屏幕显示 `SKShaderTileMode.Mirror` 如何使渐变成为备用方向。 第一个渐变是从中心的黑色到在一个半径为 100 像素的白色。 下一步是从 100 像素 radius 在 200 像素 radius 黑色到白色，再次反转的下一步的渐变。

径向渐变中，可以使用两个以上的颜色。 **彩虹 Arc 渐变**示例创建了8种颜色的数组，这些颜色对应于彩虹的颜色，以红色结束，还创建了一个包含八个位置值的数组：

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

假设画布的宽度和高度的最小值为1000，这意味着 `rainbowWidth` 值为250。 `outerRadius` 和 `innerRadius` 值分别设置为1000和750。 这些值用于计算 `positions` 数组;8个值介于 0.75 f 到1之间。 `radius` 值用于为圆形描边。 875 意味着 250 像素笔划宽度扩展 750 像素为单位的半径和 1000年像素为单位的半径之间的值：

[![彩虹弧线渐变](circular-gradients-images/RainbowArcGradient.png "彩虹弧线渐变")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

如果使用此渐变填充整个画布，会看到它是红色的内部半径范围之内。 这是因为 `positions` 数组的开头不是0。 第一种颜色用于 0 到第一个数组值的偏移量。 渐变也是红色超出外部 radius 的。 这就是 `Clamp` 平铺模式的结果。 因为渐变用于描画粗线，看不到这些红色区域。

## <a name="radial-gradients-for-masking"></a>为需要进行掩码的径向渐变

线性渐变，如径向渐变可以合并透明或部分透明的颜色。 此功能适用于称为 "_屏蔽_" 的进程，该进程隐藏部分图像以强调设计图像的另一部分。

**径向渐变掩码**页显示了一个示例。 该程序将加载一个资源位图。 "`CENTER`" 和 "`RADIUS`" 字段是从位图检查中确定的，并引用应突出显示的区域。 `PaintSurface` 处理程序首先计算一个矩形以显示位图，然后将其显示在该矩形中：

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

绘制位图后，一些简单的代码会将 `CENTER` 和 `RADIUS` 转换为 `center` 和 `radius`，这会引用位图中突出显示的区域，以供显示。 这些值用于创建该中心和 radius 与径向渐变。 两种颜色开始透明在中心和半径的第一个的 60%。 渐变然后渐变为白色：

[![径向渐变掩码](circular-gradients-images/RadialGradientMask.png "径向渐变掩码")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

此方法不屏蔽位图的最佳方式。 问题在于掩码通常具有一种颜色的白皮书，选择是为了与画布的背景相匹配。 如果背景为某种其他颜色 &mdash; 或者可能是渐变本身 &mdash; 不匹配。 [SkiaSharp Porter-Duff blend 模式](../blend-modes/porter-duff.md)一文中显示了更好的屏蔽方法。

## <a name="radial-gradients-for-specular-highlights"></a>有关反射高光的径向渐变

当光线照射到圆角的图面时，反映了在多个方向光，但光的某些直接在查看器的眼睛退回。 这通常会在图面上创建一个称为_反射高光_的模糊空白区域。

在三维图形中，反射高光通常导致用来确定浅路径和明暗度的算法。 在二维图形中，反射高光有时添加建议一个三维表面之上的外观。 镜面高光可以将转换为倒圆角的红色球平面红色圆圈。

**径向镜面高光**页面使用径向渐变来精确执行此操作。 `PaintSurface` 处理程序就是通过计算圆的半径，并使用两个 `SKPoint` 值 &mdash; `center`，`offCenter` 并将两个值介于圆心和圆的左上边缘中间：

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

`CreateRadialGradient` 调用会创建一个渐变，该渐变以白色 `offCenter` 点开始，以红色结束，以半径一半的距离结束。 它如下所示：

[![径向镜面高光](circular-gradients-images/RadialSpecularHighlight.png "径向镜面高光")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

如果您仔细查看此渐变，可能会决定它有缺陷。 渐变围绕某个特定点，并且你可能希望它就更不对称，以反映在圆角的图面。 在这种情况下，您可能更倾向于[**如下反射高光：** ](#conical-gradients-for-specular-highlights)

## <a name="the-sweep-gradient"></a>扫描渐变

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[]))方法具有所有渐变创建方法的最简单语法：

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

它是只是一个中心、 颜色、 数组和位置的颜色。 渐变的中心点右侧开始，并扫描 360 度围绕中心顺时针旋转。 请注意，没有 `SKShaderTileMode` 的参数。

具有矩阵转换参数[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))重载也可用。 若要更改的起始点的渐变到可以应用旋转转换。 此外可以应用一个比例转换，以便从顺时针方向更改为逆时针旋转。

"**扫描渐变**" 页使用扫描渐变为笔划宽度为50像素的圆形着色：

[![扫描渐变](circular-gradients-images/SweepGradient.png "扫描渐变")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage` 类定义具有不同色相值的8种颜色的数组。 请注意，数组开头和结尾 red （色调值为 0 或 360），将显示在最右侧的屏幕截图中：

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

该程序还实现了一个 `TapGestureRecognizer`，该在 `PaintSurface` 处理程序的末尾启用某些代码。 此代码使用的是同一渐变填充画布：

[![扫描梯度已满](circular-gradients-images/SweepGradientFull.png "扫描梯度已满")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

这些屏幕截图演示了渐变填充的领域着色由它。 如果尚未开始和结尾的相同的颜色渐变，将同步到中心点的右侧。

## <a name="the-two-point-conical-gradient"></a>两个点圆锥渐变

[`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有以下语法：

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

参数以中心点和半径（两个圆圈）开头，称为_开始_圆圈和_结束_圆圈。 其余三个参数与 `CreateLinearGradient` 和 `CreateRadialGradient`的参数相同。 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载包含矩阵转换。

渐变的起止日期开始圆端圆圈。 `SKShaderTileMode` 参数控制在两个圆圈外发生的情况。 两个点圆锥渐变是完全不填充区域的唯一渐变。 如果两个圆有相同的半径，渐变被限制到一个具有相同的圆圈直径的宽度的矩形。 如果两个圆有不同的半径，渐变窗体锥形区域。

您可能想要试验双点圆锥渐变，因此，**圆锥的渐变**页面派生自 `InteractivePage` 以允许两个触摸点四处移动两个圆圈半径：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

代码隐藏文件定义了两个固定半径为50和100的 `TouchPoint` 对象：

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode = 
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ? 
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }
        }
    }
}
```

`colors` 数组为红色、绿色和蓝色。 位于 `PaintSurface` 处理程序底部的代码将两个触摸点绘制为黑色圆圈，以使它们不会妨碍渐变。

请注意，`DrawRect` 调用使用渐变来为整个画布着色。 一般情况下，但是，很多画布仍未通过渐变上色。 下面是显示三个可能的配置程序：

[![圆锥渐变](circular-gradients-images/ConicalGradient.png "圆锥渐变")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

左侧的 iOS 屏幕显示了 `Clamp`的 `SKShaderTileMode` 设置的效果。 渐变开始边缘的相反端到第二个圆最接近的较小圆圈内的红色。 `Clamp` 值还会导致红色，使其继续到圆锥点。 渐变结束用蓝色的外边缘的较大圆圈，以最接近的第一个圆圈，但会继续在用蓝色该圆圈内及更高版本。

Android 屏幕类似，但具有 `Repeat``SKShaderTileMode`。 现在，梯度第一个圆圈内开始和结束之外的第二个圆圈更清晰。 "`Repeat`" 设置会导致在较大圆圈内再次向该渐变重复一次。

UWP 屏幕显示较小的圆形大圆圈内完全移动时，会发生什么情况。 梯度停止作为圆锥体和改为将填充整个区域。 效果类似于径向渐变，但如果在较大圆内不完全居中较小的圆形，则为非对称。

当一个圆圈嵌套在另一个，但它非常适合于反射的突出显示时，可能会怀疑实用用途的渐变。

## <a name="conical-gradients-for-specular-highlights"></a>有关反射高光圆锥渐变

在本文前面你已了解如何在使用径向渐变创建镜面高光。 此外可以使用两个点圆锥渐变，出于此目的，并需要它的外观：

[![圆锥反射高光](circular-gradients-images/ConicalSpecularHighlight.png "圆锥反射高光")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

非对称外观更好地建议对象的圆角的图面。 

**圆锥反射高光**页中的绘图代码与**径向反射高光**页相同（着色器除外）：

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

这两个圆圈的中心是 `offCenter` 和 `center`。 位于 `center` 上的圆圈与包围整个球的半径相关联，但位于 `offCenter` 中心的圆圈具有仅有一个像素的半径。 渐变有效地在该点开始和结束边缘的球。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
