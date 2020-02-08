---
title: 路径信息和枚举
description: 本文介绍如何获取有关 SkiaSharp 路径的信息和枚举的内容，并演示此示例代码。
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 6f4f4e6253c14d86e2057f13d6232a07a83b4d26
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045082"
---
# <a name="path-information-and-enumeration"></a>路径信息和枚举

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_获取有关路径和枚举内容的信息_

[`SKPath`](xref:SkiaSharp.SKPath)类定义多个属性和方法，它们允许你获取有关路径的信息。 [`Bounds`](xref:SkiaSharp.SKPath.Bounds)和[`TightBounds`](xref:SkiaSharp.SKPath.TightBounds)属性（及相关方法）获取路径的度量尺寸。 使用[`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single))方法可以确定特定点是否在路径中。

有时它可用于确定所有的直线和曲线构成路径的总长度。 计算此长度并不是一个算法简单的任务，因此，一个名为[`PathMeasure`](xref:SkiaSharp.SKPathMeasure)的整个类将专门用于该任务。

还有有时很有用，若要获取所有绘制操作和构成路径的点。 首先，此工具可能看起来不必要： 如果你的程序创建了路径，该程序已经知道的内容。 但是，你已了解到，路径也可以通过[路径效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)创建，并将[文本字符串转换为路径](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 此外可以获取所有绘制操作和组成这些路径的点。 一种可能性是算法转换应用到所有点，例如，若要使文字环绕半球：

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

## <a name="getting-the-path-length"></a>获取路径长度

在文章[**路径和文本**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)中，你看到了如何使用[`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint))方法来绘制一个文本字符串，该字符串的基线遵循路径的过程。 但如果你想要调整文本大小，以便精确适合的路径？ 圆环绘制文本非常简单，因为圆的周长很容易计算。 但的椭圆的周长或贝塞尔曲线的长度并不那么简单。

[`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure)类可帮助。 [构造函数](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single))接受 `SKPath` 参数，而[`Length`](xref:SkiaSharp.SKPathMeasure.Length)属性显示其长度。

此类在 "**路径长度**" 示例中进行了演示，该示例基于**贝塞尔曲线**页面。 [**PathLengthPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)文件派生自 `InteractivePage`，其中包含触摸接口：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

[**PathLengthPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)代码隐藏文件允许您移动四个触摸点以定义三次方贝塞尔曲线的终点和控制点。 三个字段定义文本字符串、`SKPaint` 对象和计算出的文本宽度：

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

"`baseTextWidth`" 字段基于 `TextSize` 设置为10的文本宽度。

`PaintSurface` 处理程序绘制贝塞尔曲线，然后调整文本大小以适应其完整长度：

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

新创建的 `SKPathMeasure` 对象的 `Length` 属性获取路径的长度。 路径长度除以 `baseTextWidth` 值（这是文本的宽度（以文本大小为10），然后再乘以基准文本大小10。 结果是显示的文本沿该路径的新文本大小：

[![](information-images/pathlength-small.png "Triple screenshot of the Path Length page")](information-images/pathlength-large.png#lightbox "Triple screenshot of the Path Length page")

贝塞尔曲线获取较长或更短，您可以看到更改文本大小。

## <a name="traversing-the-path"></a>遍历路径

`SKPathMeasure` 只需测量路径的长度。 对于介于零和路径长度之间的任何值，`SKPathMeasure` 对象可以获取路径上的位置以及该点处到路径曲线的切线。 相切作为 `SKPoint` 对象形式的矢量提供，或者作为 `SKMatrix` 对象中封装的旋转。 下面是 `SKPathMeasure` 的方法，这些方法通过各种灵活的方式获取此信息：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

[`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags)枚举的成员包括：

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

**Unicycle 半管道**页面在看上去沿三次贝赛尔曲线来回退的 Unicycle 上动画

[![](information-images/unicyclehalfpipe-small.png "Triple screenshot of the Unicycle Half-Pipe page")](information-images/unicyclehalfpipe-large.png#lightbox "Triple screenshot of the Unicycle Half-Pipe page")

用于对半管道和 unicycle 进行描边的 `SKPaint` 对象定义为 `UnicycleHalfPipePage` 类中的字段。 此外，还定义了 unicycle 的 `SKPath` 对象：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

类包含 `OnAppearing` 的标准替代和动画的 `OnDisappearing` 方法。 `PaintSurface` 处理程序创建半管道的路径，然后绘制该路径。 然后，将基于此路径创建一个 `SKPathMeasure` 对象：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

`PaintSurface` 处理程序计算每五秒从0到1的 `t` 值。 然后，它使用 `Math.Cos` 函数将其转换为0到1之间的 `t` 值，并返回到0，其中0对应于左上方的 unicycle，而1对应于右上方的 unicycle。 余弦函数会导致要慢的放在管道的顶部和底部最快的速度。

请注意，`t` 的此值必须与要 `GetMatrix`的第一个参数的路径长度相乘。 然后，将矩阵应用于用于绘制 unicycle 路径的 `SKCanvas` 对象。

## <a name="enumerating-the-path"></a>枚举路径

`SKPath` 的两个嵌入类可用于枚举路径的内容。 [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator)和[`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator)这些类。 这两个类非常相似，但 `SKPath.Iterator` 可以消除路径中长度为零的元素，或接近零长度。 下面的示例使用 `RawIterator`。

可以通过调用 `SKPath`的[`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator)方法来获取 `SKPath.RawIterator` 类型的对象。 枚举路径是通过重复调用[`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*)方法来完成的。 向其传递四个 `SKPoint` 值的数组：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next` 方法返回[`SKPathVerb`](xref:SkiaSharp.SKPathVerb)枚举类型的成员。 这些值表示特定的绘图命令的路径中。 插入数组中的有效点数目取决于此谓词：

- 使用单个点 `Move`
- 具有两个点的 `Line`
- 带四个点的 `Cubic`
- 带三个点的 `Quad`
- 带三个点的 `Conic` （并同时调用[`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*)方法以获取权重）
- `Close`，其中包含一个点
- `Done`

`Done` 谓词指示路径枚举已完成。

请注意，不存在任何 `Arc` 谓词。 这表示所有弧线将都转换为贝塞尔曲线时添加到的路径。

`SKPoint` 数组中的某些信息是冗余的。 例如，如果 `Move` 谓词后跟 `Line` 谓词，则 `Line` 附带的两个点中的第一个点与 `Move` 点相同。 在实践中，此冗余是非常有帮助。 获取 `Cubic` 谓词时，将附带定义三次方贝塞尔曲线的四个点。 不需要保留当前的位置由上一个谓词。

但有问题的动词是 `Close`的。 此命令绘制从当前位置到 `Move` 命令之前建立的等高线开头的直线。 理想情况下，`Close` 谓词应提供这两个点，而不只是一个点。 更糟的是，`Close` 谓词附带的点始终为（0，0）。 当您枚举路径时，您可能需要保留 `Move` 点和当前位置。

## <a name="enumerating-flattening-and-malforming"></a>枚举、 平展和 Malforming

属性有时更加理想应用算法转换到错误的路径以某种方式：

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

大多数这些字母包含的直线，但这些直线具有显然已篡改成曲线。 这是如何实现？

关键是原始的直线被拆分为一系列的较小的直线。 然后可以以不同方式形成一条曲线操作这些单独的较小直线。

为了帮助进行此过程， [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例包含一个静态[`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs)类，该类具有一个 `Interpolate` 方法，该方法将一条直线向下划分为长度仅为一个单位的多个短行。 此外，该类包含三种类型的贝塞尔曲线转换为一系列的近似曲线的小直线的几种方法。 （参数化公式出现在[**三种类型的贝塞尔曲线**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)中。）此过程称为 "_平展_曲线"：

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

所有这些方法都是从扩展方法中引用 `CloneWithTransform` 也包含在此类中，如下所示。 此方法通过枚举路径命令和构造基于的数据的新路径克隆一个路径。 但是，新路径只包含 `MoveTo` 和 `LineTo` 调用。 所有曲线和直线被都减少到一系列小的行。

调用 `CloneWithTransform`时，会将 `Func<SKPoint, SKPoint>`传递给方法，该方法具有返回 `SKPoint` 值 `SKPaint` 参数的函数。 每个点来应用自定义算法转换为调用此函数：

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

克隆的路径减少到很小的直线，因为转换函数将已转换为曲线的直线，直线的功能。

请注意，该方法将在名为 `firstPoint` 的变量中保留每个轮廓的第一个点，并且变量 `lastPoint`中的每个绘图命令后的当前位置。 如果遇到 `Close` 谓词，则需要使用这些变量来构造最终的右行。

**GlobularText**示例使用此扩展方法，看起来围绕3d 效果中的半球环绕文本：

[![](information-images/globulartext-small.png "Triple screenshot of the Globular Text page")](information-images/globulartext-large.png#lightbox "Triple screenshot of the Globular Text page")

[`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs)类构造函数执行此转换。 它为文本创建 `SKPaint` 的对象，然后从 `GetTextPath` 方法获取 `SKPath` 对象。 这是传递到 `CloneWithTransform` 扩展方法以及转换函数的路径：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

转换函数首先计算两个名称分别为 "`longitude`" 和 "`latitude`"，范围从文本的顶部和左侧的–π/2 到文本右侧的π/2。 这些值的范围不直观地令人满意，因此它们减少乘以 0.75。 （请尝试不使用这些调整代码。 文本在北和南两极变得太难懂，边太窄。）这些三维球状坐标将转换为二维 `x`，并按标准公式 `y` 坐标。

作为字段存储的新路径。 然后，`PaintSurface` 处理程序只需将路径居中和缩放即可显示在屏幕上：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

这是非常通用技术。 如果[**路径效果**](effects.md)一文中所述的路径效果数组并未包含您认为应该包含的内容，则这是一种填充空白的方法。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
