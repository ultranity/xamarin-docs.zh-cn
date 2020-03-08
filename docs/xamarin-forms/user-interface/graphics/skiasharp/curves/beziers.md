---
title: 贝塞尔曲线的三种类型
description: 此文介绍了如何使用 SkiaSharp 呈现在 Xamarin.Forms 应用程序中的三次方、 二次，和圆锥贝塞尔曲线，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 1cf061f2ff27720ad78567bc26f00d99c5456f04
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916412"
---
# <a name="three-types-of-bzier-curves"></a>贝塞尔曲线的三种类型

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 呈现立方、二次和圆锥次贝塞尔曲线_

圣皮埃尔贝塞尔 （1910年 – 1999 年），汽车公司 Renault，计算机辅助设计的汽车正文使用曲线的法语工程师命名贝塞尔曲线。

贝塞尔曲线非常适合于交互式设计：它们的行为良好 &mdash; 换言之，singularities 不会导致曲线变得无限大或难以 &mdash;，并且通常具

![样本贝塞尔曲线](beziers-images/beziersample.png)

贝塞尔曲线通常定义字符轮廓的基于计算机的字体。

[**贝塞尔曲线**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)上的维基百科文章包含一些有用的背景信息。 术语 "*贝塞尔曲线*" 实际上指的是一系列相似的曲线。 SkiaSharp 支持三种类型的贝赛尔曲线，称为*三*类、*二*次和*圆锥*。 圆锥也称为*有理数二次*。

## <a name="the-cubic-bzier-curve"></a>三次方贝塞尔曲线

中的三次是大多数开发人员认为当贝塞尔曲线的使用者提供的贝塞尔曲线的类型。

您可以使用具有三个 `SKPoint` 参数的[`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint))方法将三次贝塞尔曲线添加到 `SKPath` 对象，或使用单独的 `x` 和 `y` 参数将[`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single))重载添加到其中：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

当前点轮廓线的曲线开始。 完成三次方贝塞尔曲线由四个点定义：

- 起点：等高线中的当前点，如果未调用 `MoveTo`，则为（0，0）
- 第一个控制点： `CubicTo` 调用中的 `point1`
- 第二个控制点： `CubicTo` 调用中的 `point2`
- 终结点： `CubicTo` 调用中的 `point3`

生成的曲线的开始处开始并结束在终结点。 曲线通常不会经历的两个控制点;改为两个控制点更像磁铁拉取针对它们的曲线函数。

了解的三次方贝塞尔曲线的最佳方式是通过试验。 这是**贝塞尔曲线**页面的目的，它派生自 `InteractivePage`。 [**BezierCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)文件实例化 `SKCanvasView` 和 `TouchEffect`。 [**BezierCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)代码隐藏文件在其构造函数中创建四个 `TouchPoint` 的对象。 `PaintSurface` 事件处理程序将创建一个 `SKPath`，用于基于四个 `TouchPoint` 对象呈现贝塞尔曲线，还绘制从控制点到终点的点线切线：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

运行此处：

[贝塞尔曲线页面的 ![三次屏幕截图](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

从数学上，该曲线是三次多项式的次数。 曲线最相交处的三个点的直线。 在开始点，该曲线始终是第一个控制点的切线，然后在相同的方向，一条直线从一开始点。 在终结点，该曲线始终是终结点的切线，然后在相同的方向，一条直线从第二个管理点。

三次方贝塞尔曲线始终受连接四个点凸四边形。 这称为凸形*凸*形。 如果起点和终点之间的直线上位于控点，然后贝赛尔曲线将呈现为一条直线。 但如第三个屏幕截图中所示，曲线可以还跨本身。

路径 contour 可以包含多个连接三次方贝塞尔曲线，但两条三次方贝塞尔曲线之间的连接将仅当以下三个点共线平滑 （即，位于上一条直线）：

- 第一条曲线的第二个控制点
- 第一条曲线，它也是第二条曲线的起始点的终结点
- 第二条曲线的第一个控制点

在下一篇文章中，你将发现[**一项可**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)简化平滑连接的 bezier 曲线定义的工具。

有时它可用于了解呈现三次方贝塞尔曲线的基础参数等式。 对于*t* （范围从0到1），参数公式如下所示：

点 = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

最高的 3 指数确认这些是三次方 polynomials。 在 `t` 等于0时，点为（x ₀，y ₀），即开始点，并且当 `t` 等于1时，点为（x ₃，y ₃），即终结点。 接近开始点（对于 `t`的低值），第一个控制点（x ₁，y ₁）具有很强的效果，接近终结点（"t"）时，第二个控制点（x ₂，y ₂）有很大的影响。

## <a name="bezier-curve-approximation-to-circular-arcs"></a>贝塞尔曲线的近似圆弧

有时使用 Bezier 曲线来渲染圆弧会很方便。一条三次贝塞尔曲线可以使圆弧非常接近于一个季度圆，因此，四个已连接的贝塞尔曲线可以定义一个圆形。 在发布超过 25 年的两篇文章中讨论此近似值：

> Tor Dokken，al al，"通过曲率准确的圆量-连续贝塞尔曲线"，"*计算机辅助几何设计 7* （1990），33-41"。

> Michael Goldapp，"Polynomials，"*计算机辅助几何设计 8* （1991），227-238。

下图显示了四个标记为 `pto`、`pt1`、`pt2`和 `pt3` 的点，定义了一个与圆弧曲线（显示为红色）的点：

![具有贝塞尔曲线的圆弧的近似值](beziers-images/bezierarc45.png)

起点和终点到控制点的直线分别与圆圈和贝塞尔曲线相切，而且其长度为*L*。上面提到的第一篇文章指出，在计算此长度*L*时，贝塞尔曲线最接近圆弧：

L = 4 × tan(α / 4) / 3

因此 L 等于 0.265 图中显示的 45 度的角度。 在代码中，此值将乘以圆的所需的半径。

使用 "**贝塞尔圆弧**" 页，您可以尝试定义一条贝塞尔曲线，以使圆圆弧的角度接近于180度。 [**BezierCircularArcPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)文件实例化 `SKCanvasView` 和用于选择角度的 `Slider`。 [**BezierCircularArgPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)代码隐藏文件中的 `PaintSurface` 事件处理程序使用转换将点（0，0）设置为画布的中心。 它绘制圆形以进行比较，该点为中心，然后计算贝赛尔曲线的两个控制点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

起点和终点（`point0` 和 `point3`）是根据圆圈的普通参数方程来计算的。 因为圆的中心 （0，0），这些点可以也被视为径向向量从圆的中心到周长。 控制点是在行的正切值为圆圈，因此可以为这些径向向量直角。 直角到另一个向量是只需使用交换的 X 和 Y 坐标和负所做的其中一个原始向量。

下面是运行使用不同的角度的程序：

[贝塞尔圆弧页面的 ![三次屏幕截图](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

仔细查看第三个屏幕截图中，并且将看到贝赛尔曲线值得注意的是偏离一个半圆，当角度 180 度，但 iOS 屏幕显示，它看起来非常适合每个季度圆圈可以很好地时表示的角度为 90 度。

四分之一圆面向此类时，则计算两个控制点的坐标是非常简单：

![贝塞尔曲线的近似值](beziers-images/bezierarc90.png)

如果圆的半径为100，则*L*为55，这是一个容易记住的数字。

"**圆**" 页将在圆与正方形之间对图形进行动画处理。 圆圈的近似方式为四个贝塞尔曲线，其坐标显示在[`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs)类中此数组定义的第一列中：

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

第二列包含四个定义其区域大约是圆的面积相同的正方形的贝塞尔曲线的坐标。 （使用与给定圆圈*完全*相同的区域绘制正方形就是[无法解决的典型几何问题。）](https://en.wikipedia.org/wiki/Squaring_the_circle)对于使用 Bezier 曲线渲染正方形，每条曲线的两个控制点是相同的，它们与起点和终点 colinear，因此 Bezier 曲线呈现为直线。

有关动画的内插值是数组的第三个列。 此页将为16毫秒设置计时器，并按该速率调用 `PaintSurface` 处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

根据 `t`的 sinusoidally 振荡值将点插值。 内插的点然后用于构造一系列的四个已连接的贝塞尔曲线。 下面是运行动画：

[用于求圆圈页的求平方的 ![三个屏幕截图](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

此类动画就不可能无需通过算法灵活，可以呈现为循环弧线和直线的曲线。

**贝塞尔无穷**页还利用贝塞尔曲线的功能来接近圆弧。下面是[`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs)类中的 `PaintSurface` 处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

它可能是非常好的练习来绘制图形纸上的这些坐标以查看它们如何相关。 无穷符号居中围绕点 （0，0），并在两个循环使用的中心 （–150，0） 和 （150，0） 和半径为 100。 在 `CubicTo` 的命令系列中，可以看到对值为–95和–205（这些值为–150加和减55）、205和95（150 + 和减号55）以及250和–250的控制点的 X 坐标。 唯一的例外是无穷符号有超过本身在中心。 在这种情况下，控制点具有 50 和 – 50 若要查看中心附近的曲线下的组合使用的坐标。

下面是无穷符号：

[贝塞尔无穷页的 ![三次屏幕截图](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

与 "**圆弧无穷**" 页面呈现的无穷符号相比，从[**三种方法绘制弧形**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章中，它的距离略有不同。

## <a name="the-quadratic-bzier-curve"></a>二次贝塞尔曲线

二次贝塞尔曲线具有只有一个控制点，并且只需三个点定义曲线： 起始点、 控制点和终结点。 以下参数方程确定是为三次方贝塞尔曲线，非常类似，只不过最高的指数是 2，因此曲线是二次多项式的次数：

点 = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

若要将二次贝塞尔曲线添加到路径中，请使用[`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint))方法或具有单独 `x` 和 `y` 坐标的[`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single))重载：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法从当前位置添加曲线，以将 `point1` 作为控制点 `point2`。

您可以用**二次曲线**页面试验二次贝塞尔曲线，这与**贝塞尔曲线**页面非常相似，只不过它只有三个触点。 下面是[**QuadraticCurve.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs)代码隐藏文件中的 `PaintSurface` 处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

和此处运行：

[![二次曲线页面的三向屏幕截图](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

点线是为在起点和终点，曲线的正切值和满足在控点。

如果所需的常规形状，曲线，但需要的只是一个管理点，而不是两个方便起见，二次贝塞尔曲线是很好。 二次贝塞尔曲线比任何其他曲线，这正是它在内部用于在 Skia 呈现椭圆弧形更高效地呈现。

但是，二次贝塞尔曲线的形状不是椭圆形，这就是为什么需要多次二次 Béziers 来接近椭圆弧。次贝塞尔是 parabola 的一段。

## <a name="the-conic-bzier-curve"></a>圆锥贝塞尔曲线

圆锥贝赛尔曲线 &mdash; 也称为 "有理二次贝塞尔曲线" &mdash; 是相对较新的贝塞尔曲线系列。 二次贝塞尔曲线，如合理的二次贝塞尔曲线涉及到起始点、 终结点和一个控制点。 但有理二次贝塞尔曲线还需要一个*权重*值。 它被称为*有理数*，因为参数公式涉及比率。

以下参数方程确定 X 和 Y 是共享相同的分母的比率。 下面是*t*的分母的公式，范围为0到1，权重值为*w*：

d(t) = (1 – t) ² + 2wt(1 – t) + t²

从理论上讲，合理的二次可能涉及到三个单独的权重值，一个用于每个这三个术语，但这些可以简化为只是一个中间一词的权重值。

只不过中间术语还包括权重值，并且表达式除以分母的 X 和 Y 坐标的以下参数方程确定是类似于二次贝塞尔曲线的以下参数方程确定：

点 = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

合理的二次贝塞尔曲线也称为*conics* ，因为它们可以精确表示任意圆锥部分的段，&mdash; hyperbolas、parabolas、省略号和圆圈。

若要将有理二次贝塞尔曲线添加到路径中，请使用[`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single))方法或具有单独 `x` 和 `y` 坐标的[`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single))重载：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

请注意最后一个 `weight` 参数。

"**圆锥曲线**" 页允许您试验这些曲线。 `ConicCurvePage` 类派生自 `InteractivePage`。 [**ConicCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)文件实例化一个 `Slider`，以选择–2到2之间的一个权重值。 [**ConicCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)代码隐藏文件创建三个 `TouchPoint` 对象，而 `PaintSurface` 处理程序只是将具有切线的结果曲线渲染到控制点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

运行此处：

[圆锥曲线页面的 ![三次屏幕截图](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

正如您所看到的看起来的权重是更高版本时拉取向更多的曲线的控制点。 权重为零，曲线将成为一条直线的起始点从终结点。

理论上，允许负权重，并使曲线*远离*控制点。 但是，-1 或更低的权重会导致参数方程中的分母对于*t*的特定值为负。 此原因可能导致在 `ConicTo` 方法中忽略负值。 使用**圆锥曲线**计划可以设置负值，但正如您通过试验所看到的那样，负权重具有与零权重相同的效果，并导致呈现直线。

可以很容易地通过派生控制点和权重来使用 `ConicTo` 方法将圆弧绘制到（但不包括）半圆上。 在下图中，从起点和终点的切线满足在控点。

![圆弧的圆锥弧线渲染](beziers-images/conicarc.png)

可以使用三角函数来确定从圆的中心管理点的距离： 它是半角度 α 的余弦值除以圆的半径。 若要开始和结束点之间绘制一个圆弧，将权重设置为该同一一半角度的余弦。 请注意，是否角是 180 度，然后切线永远不会满足和权重为零。 但对于角度小于 180 度、 数学计算工作正常。

"**圆锥**圆弧" 页演示了这一点。 [**ConicCircularArc**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)文件实例化用于选择角度的 `Slider`。 [**ConicCircularArc.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)代码隐藏文件中的 `PaintSurface` 处理程序计算控制点和权重：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

正如您所看到的，显示为红色的 `ConicTo` 路径与显示为引用的基础圆圈之间没有直观的区别：

[圆锥圆弧页面的 ![三次屏幕截图](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

但设置为 180 度和数学失败的角度。

在这种情况下，`ConicTo` 不支持负权重，因为在理论上（基于参数方程），可以通过对 `ConicTo` 使用同一点但使用负值的负值来完成圆。 这将允许基于（但不包括）0度和180度之间的任意角度，创建一个包含两个 `ConicTo` 曲线的圆。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
