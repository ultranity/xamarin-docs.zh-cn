---
title: SkiaSharp 线性渐变
description: 了解如何绘制笔画行或使用渐变填充区域组成的两种颜色渐变的混合。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 290e533e54b2ee150b94d9fb6b0f5119324f9cf0
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916379"
---
# <a name="the-skiasharp-linear-gradient"></a>SkiaSharp 线性渐变

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKPaint`](xref:SkiaSharp.SKPaint)类定义一个[`Color`](xref:SkiaSharp.SKPaint.Color)属性，该属性用于对线条进行描边或用纯色填充区域。 您还可以通过渐变线条或用_渐变_填充区域，这是一种逐渐混合的颜色：

![线性渐变示例](linear-gradient-images/LinearGradientSample.png "线性渐变示例")

最基本的渐变类型是_线性_渐变。 颜色混合出现在直线上（称为_渐变线_）。 垂直渐变线的行具有相同的颜色。 使用两个静态[`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*)方法之一创建线性渐变。 两个重载之间的区别是一个包括矩阵转换，另一个不。 

这些方法返回[`SKShader`](xref:SkiaSharp.SKShader)的类型的对象，该对象设置为 `SKPaint`的[`Shader`](xref:SkiaSharp.SKPaint.Shader)属性。 如果 `Shader` 属性为非 null，则它将覆盖 `Color` 属性。 描边的任何线条或使用此 `SKPaint` 对象填充的任何区域都基于渐变而不是纯色。

> [!NOTE]
> 在 `DrawBitmap` 调用中包含 `SKPaint` 对象时，将忽略 `Shader` 属性。 您可以使用 `SKPaint` 的 `Color` 属性设置显示位图的透明度级别（如[显示 SkiaSharp 位图](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)一文中所述），但不能使用 `Shader` 属性来显示具有渐变透明度的位图。 其他方法可用于显示具有渐变透明度的位图：文章[SkiaSharp 循环渐变](circular-gradients.md#radial-gradients-for-masking)和[SkiaSharp 合成模式和混合模式](../blend-modes/porter-duff.md#gradient-transparency-and-transitions)。

## <a name="corner-to-corner-gradients"></a>角角渐变

通常的线性渐变从一个矩形的角扩展到另一个。 如果的起点是矩形的左上角，可以扩展渐变：

- 沿垂直方向与左下角
- 沿水平方向与右上角
- 到右下角沿对角线方向

对角线性渐变在 SkiaSharp 着色器的第一页和[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**其他效果**部分中进行了演示。 **角部渐变**页面在其构造函数中创建 `SKCanvasView`。 `PaintSurface` 处理程序在 `using` 语句中创建了一个 `SKPaint` 对象，然后定义了一个中心于画布的300像素正方形矩形：

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

`SKPaint` 的 `Shader` 属性分配有静态 `SKShader.CreateLinearGradient` 方法的 `SKShader` 返回值。 五个参数是按如下所示：

- 矩形的左上角，此处设置的渐变的开始点
- 渐变的终点的矩形的右下角，此处设置
- 两个或多个颜色渐变构成的数组
- `float` 值的数组，这些值指示渐变线内颜色的相对位置
- [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode)枚举的成员，指示渐变的行为如何超出渐变线的末端

创建渐变对象后，`DrawRect` 方法将使用包括着色器的 `SKPaint` 对象绘制300像素正方形矩形。 此处它在 iOS、 Android 和通用 Windows 平台 (UWP) 上运行：

[![角部到角渐变](linear-gradient-images/CornerToCornerGradient.png "角部到角渐变")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

渐变线作为前两个参数指定的两个点定义。 请注意，这些点相对于_画布_，而_不_是与渐变一起显示的图形对象。 沿渐变线颜色逐渐转换从左上角为蓝色在右下角的红色。 垂直渐变线的任何行具有常量颜色。

指定为第四个参数的 `float` 值数组与颜色数组之间具有一对一的对应关系。 这些值表示发生这些颜色沿渐变线的相对位置。 此处，0表示 `Red` 在渐变线的开头发生，1表示 `Blue` 出现在行的末尾。 数字必须升序排序，并应在 0 到 1 范围内。 如果它们不在该范围内，它们将调整为在该范围内。

非 0 和 1，可以为内容设置数组中的两个值。 试试看：

```csharp
new float[] { 0.25f, 0.75f }
```

渐变线的整个第一季度现在是纯红色，并最后一个季度是纯蓝色。 红色和蓝色的组合被限制为渐变线的中心部分。

通常情况下，你将想要空间这些位置值同样从 0 到 1。 如果是这种情况，只需将 `null` 作为第四个参数提供给 `CreateLinearGradient`。

尽管两个 300 像素正方形矩形的角之间定义了此渐变，但不限于填充该矩形。 **角部到角渐变**页面包含一些额外的代码，这些代码可响应页面上的点击或鼠标单击。 每点击一次，`drawBackground` 字段在 `true` 和 `false` 之间切换。 如果 `true`值，则 `PaintSurface` 处理程序将使用相同的 `SKPaint` 对象来填充整个画布，然后绘制一个黑色矩形，指示较小的矩形： 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
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
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

下面是点击屏幕后将看到什么：

[![角部到角梯度已满](linear-gradient-images/CornerToCornerGradientFull.png "角部到角梯度已满")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

请注意，渐变中都会重复相同的模式之外定义渐变线的点。 发生此重复的原因是 `CreateLinearGradient` 的最后一个参数 `SKShaderTileMode.Repeat`。 （稍后您将看到其他选项。）

另请注意，用于指定渐变线的点并不唯一。 垂直渐变线的行具有相同的颜色，因此有无限数目的可以为相同的效果指定的渐变线。 例如时使用的水平渐变填充矩形，, 您可以指定的左上角和右上角，或的左下角和右下角或甚至与和并行这些行的任何两个点。

## <a name="interactively-experiment"></a>以交互方式试验

可以通过**交互式线性渐变**页面以交互方式试验线性渐变。 本页使用[**第三种方法来绘制弧形**](../../curves/arcs.md)中引入的 `InteractivePage` 类。`InteractivePage` 处理[`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)事件以维护 `TouchPoint` 对象的集合，您可以使用手指或鼠标移动这些对象。

XAML 文件将 `TouchEffect` 附加到 `SKCanvasView` 的父项，还包括一个 `Picker`，该允许您选择[`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode)枚举的三个成员之一：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
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

代码隐藏文件中的构造函数为线性渐变的起点和终点创建两个 `TouchPoint` 的对象。 `PaintSurface` 处理程序定义了三种颜色的数组（用于从红色到绿色到蓝色的渐变），并从 `Picker`获取当前 `SKShaderTileMode`：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

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
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

`PaintSurface` 处理程序从所有这些信息创建 `SKShader` 对象，并使用它来为整个画布着色。 `float` 值的数组设置为 `null`。 否则，以同样空间三种颜色，会将该参数设置为 0、 0.5 和 1 的值的数组。

`PaintSurface` 处理程序的大部分专门用于显示多个对象：触控点为轮廓圆圈、渐变线条和与触控点垂直的渐变线条：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
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

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

连接两个接触点的渐变线是轻松地绘制，但垂直行需要更多工作。 渐变线是转换为一个向量，规范化一个单位的长度，然后旋转 90 度。 该向量随后将授予的长度为 200 像素。 它用于绘制从触摸点为垂直渐变线于扩展的四行。

垂直的行的开头和结尾的渐变与一致。 这些行以外的情况取决于 `SKShaderTileMode` 枚举的设置：

[![交互式线性渐变](linear-gradient-images/InteractiveLinearGradient.png "交互式线性渐变")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

这三个屏幕截图显示了[`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode)三个不同值的结果。 IOS 屏幕快照显示 `SKShaderTileMode.Clamp`，只是扩展渐变边框上的颜色。 Android 屏幕截图中的 "`SKShaderTileMode.Repeat`" 选项显示如何重复渐变模式。 UWP 屏幕截图中的 "`SKShaderTileMode.Mirror`" 选项也会重复模式，但每次都会反转模式，导致不会出现颜色中断。

## <a name="gradients-on-gradients"></a>在渐变的渐变

`SKShader` 类不定义除 `Dispose`以外的公共属性或方法。 因此，通过其静态方法创建的 `SKShader` 对象是不可变的。 即使为两个不同的对象使用的是同一渐变，很可能您需要略有不同渐变。 为此，您需要创建一个新的 `SKShader` 对象。

"**渐变文本**" 页显示带有类似渐变的彩色文本和后台：

[![渐变文本](linear-gradient-images/GradientText.png "渐变文本")](linear-gradient-images/GradientText-Large.png#lightbox)

在渐变中的唯一差别是起点和终点。 用于显示文本使用的渐变基于文本的边框圆角的两个点。 有关背景信息，两个点基于整个画布。 代码如下：

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

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
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

首先设置 `SKPaint` 对象的 `Shader` 属性，以显示渐变以覆盖背景。 渐变点设置为在画布的左上角和右下角。

此代码将设置 `SKPaint` 对象的 `TextSize` 属性，使文本显示在画布宽度的90%。 文本边界用于计算要传递给 `DrawText` 方法的 `xText` 和 `yText` 值以使文本居中。

但是，第二个 `CreateLinearGradient` 调用的渐变点必须指文本在显示时相对于画布的左上角和右下角。 这是通过将 `textBounds` 矩形移位 `xText` 和 `yText` 值来实现的：

```csharp
textBounds.Offset(xText, yText);
```

现在该矩形的左上角和右下角可以用于设置起点和终点的渐变。

## <a name="animating-a-gradient"></a>对渐变进行动画处理

有几种方法进行动画处理渐变。 一种方法是对起点和终点进行动画处理。 **渐变动画**页面在画布上中心的圆圈内移动两个点。 此圆的半径为一半宽度或高度的画布，两者中较小。 起点和终点在此圆圈上彼此相反，渐变从白色到黑色，使用 `Mirror` 平铺模式：

[![渐变动画](linear-gradient-images/GradientAnimation.png "渐变动画")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

构造函数创建 `SKCanvasView`。 `OnAppearing` 和 `OnDisappearing` 方法处理动画逻辑：

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

`OnTimerTick` 方法计算每3秒从0到2π的动画 `angle` 值。 

下面是一种方法来计算两个渐变点。 将计算名为 `vector` 的 `SKPoint` 值，以将其从画布中心延伸到圆圈半径上的某个点。 此向量的方向取决于表示的角度的正弦和余弦值。 然后计算两个相反渐变点： 一个点是通过减去从中心点，该矢量和其他点计算通过将向量添加到中心点：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

稍有不同的方法要求更少的代码。 此方法使用带有矩阵转换的[`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载方法作为最后一个参数。 此方法是[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中的版本：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

如果画布的宽度小于高度，则将两个渐变点设置为（0，0）和（`info.Width`，0）。 作为最后一个参数传递的旋转转换 `CreateLinearGradient` 可以有效地围绕屏幕中心旋转这两个点。

请注意，如果表示的角度为 0，则没有旋转，两个渐变点是在画布的左上角和右上角。 这些点不是按前面 `CreateLinearGradient` 调用中所示计算的相同渐变点。 但这些点_平行_于 bisects 画布中心的水平渐变线，它们会产生相同的渐变效果。

**彩虹渐变**

**彩虹渐变**页面从画布的左上角到右下角绘制一个彩虹。 但此彩虹渐变不是真正出喷薄彩虹等。 它是直线，而不是平的但基于由循环色调值从 0 到 360 之间的八个 HSL （色调-饱和度-亮度） 颜色：

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

该代码属于下面所示的 `PaintSurface` 处理程序。 该处理程序首先创建定义扩展从画布的左上角到右下角的六面多边形的路径：

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

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

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

`CreateLinearGradient` 方法中的两个渐变点基于定义此路径的两个点：两个点接近左上角。 第一种是在画布上边缘和第二个是画布的左边缘。 结果如下：

[![彩虹梯度错误](linear-gradient-images/RainbowGradientFaulty.png "彩虹梯度错误")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

这是一个有趣的映像，但并不相当的意图。 问题在于，当创建线性渐变，常量颜色的行被垂直于渐变线。 渐变线基于其中图涉及顶部和左侧边，并且该行通常不是将扩展到右下角的图边缘与垂直的点。 仅当在画布已方形，此方法将适用。

若要创建正确的彩虹渐变，渐变线必须垂直出喷薄彩虹的边缘。 这是一个更复杂的计算。 一个向量，必须定义与长边的图并行。 矢量是旋转 90 度，以便与该端垂直。 然后，通过将其与 `rainbowWidth`相乘来延长图形的宽度。 两个渐变点是根据上图中的一侧的点以及计算以及向量的点。 下面是在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**彩虹渐变**页面中显示的代码：

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

现在出喷薄彩虹颜色与图一致：

[![彩虹渐变](linear-gradient-images/RainbowGradient.png "彩虹渐变")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**无穷色**

"**无限大颜色**" 页中也使用彩虹渐变。 此页使用[**第三种贝塞尔曲线类型**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs)一文中所述的路径对象来绘制无穷号。 然后以与持续扫描在图像动画出喷薄彩虹渐变着色图像。

构造函数创建描述无穷符号的 `SKPath` 对象。 创建路径后，构造函数还可以获取路径的矩形边界。 然后，它将计算名为 `gradientCycleLength`的值。 如果渐变基于 `pathBounds` 矩形的左上角和右下角，则此 `gradientCycleLength` 值为渐变模式的总水平宽度：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

构造函数还为彩虹和 `SKCanvasView` 对象创建了 `colors` 数组。

重写 `OnAppearing` 和 `OnDisappearing` 方法会对动画产生开销。 `OnTimerTick` 方法将 `offset` 字段从0动画动画处理为每两秒 `gradientCycleLength`：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

最后，`PaintSurface` 处理程序呈现无穷号。 由于路径包含围绕中心点（0，0）的正片坐标和正坐标，画布上的 `Translate` 变换用于将其移到中心。 转换转换后跟一个 `Scale` 转换，该转换应用一个缩放因子，使无穷符号尽可能大，同时仍保持在画布的宽度和高度的95% 范围内。 

请注意，`STROKE_WIDTH` 常数添加到了路径边框的宽度和高度。 因此呈现的无穷大大小的大小增加了所有四个边的宽度的一半，路径将描边与此宽度的行：

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

查看作为 `SKShader.CreateLinearGradient`的前两个参数传递的点。 这些点基于边界矩形的原始路径。 第一点是（&ndash;250，&ndash;100），第二个点为（250，100）。 SkiaSharp 的内部，这些点受制于当前的画布转换以便他们能够正确对齐以显示的无穷符号。

如果没有 `CreateLinearGradient`的最后一个参数，您将看到一个从无穷符号左上角到右下角的彩虹梯度。 （实际上，渐变扩展从左上角到右下角的边框。 呈现的无穷符号大于边框，其两边 `STROKE_WIDTH` 值的一半。 因为渐变在开始和结束时都是红色的，而渐变是用 `SKShaderTileMode.Repeat`创建的，所以差异并不明显。）

利用 `CreateLinearGradient`的最后一个参数，渐变模式可以在图像中持续扫描：

[![无穷色](linear-gradient-images/InfinityColors.png "无穷色")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>透明和渐变

参与为渐变的颜色可以包含的透明度。 而不是从一种颜色渐变到另一个渐变，渐变可以淡出从一种颜色为透明。 

此方法可用于一些有趣的效果。 一个典型的示例显示了具有其反射的图形对象：

[![反射渐变](linear-gradient-images/ReflectionGradient.png "反射渐变")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

颠倒的文本将显示为透明顶部到底部完全透明的 50%的渐变。 这些级别不透明度与相关联的 0x80 alpha 值和 0。

"**反射渐变**" 页中的 `PaintSurface` 处理程序将文本的大小缩放为画布宽度的90%。 然后，它将计算 `xText` 和 `yText` 值以将文本水平居中，但置于相对于页面垂直中心的基线上：

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

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
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

这些 `xText` 和 `yText` 值是用于显示 `PaintSurface` 处理程序底部的 `DrawText` 调用中的反射文本的相同值。 但在该代码之前，您将看到对 `SKCanvas`的 `Scale` 方法的调用。 此 `Scale` 方法水平缩放1（不执行任何操作），但通过 &ndash;1 进行垂直缩放，这会有效地翻转所有内容。 旋转中心设置为点（0，`yText`），其中 `yText` 是画布的垂直中心，最初计算为 `info.Height` 除以2。

请记住，Skia 使用渐变颜色在画布转换之前的图形对象。 绘制 unreflected 文本后，将移动 `textBounds` 矩形，使其对应于显示的文本：

```csharp
textBounds.Offset(xText, yText);
```

`CreateLinearGradient` 调用定义了从该矩形顶部到底部的渐变。 渐变是从完全透明的蓝色（`paint.Color.WithAlpha(0)`）到50% 透明蓝色（`paint.Color.WithAlpha(0x80)`）。 画布转换翻转文本颠倒，因此 50%透明蓝色开始基线，并将变为透明顶部的文本。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
