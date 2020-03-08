---
title: Porter Duff 的混合模式
description: 使用 Porter Duff 的混合模式来编写基于源和目标图像的场景。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c15dd4606a75cc3cdffbad71f15299568157213a
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915969"
---
# <a name="porter-duff-blend-modes"></a>Porter Duff 的混合模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Thomas Porter 和 Tom Duff，他们负责开发组合的情况下在 Lucasfilm 代数后命名 Porter Duff 的混合模式。 在_计算机图形_7 月1984问题中发布了其纸张[_合成数字图像_](https://graphics.pixar.com/library/Compositing/paper.pdf)，页面253到259。 这些混合模式是组合的情况下，这组合到复合场景的各种映像的基础：

![Porter-Duff 示例](porter-duff-images/PorterDuffSample.png "Porter-Duff 示例")

## <a name="porter-duff-concepts"></a>Porter Duff 概念

假设 brownish 矩形所占的显示图面左侧和顶部 2 / 3:

![Porter-Duff 目标](porter-duff-images/PorterDuffDst.png "Porter-Duff 目标")

此区域称为 "_目标_" 或 "有时是背景_或背景"。_

你想要绘制以下矩形的大小相同的目标。 该矩形是透明占据右侧和底部的三分之二的色调蓝色区域除外：

![Porter-Duff Source](porter-duff-images/PorterDuffSrc.png "Porter-Duff Source")

这称为_源_或_前台_。

当在目标上显示的源代码时，下面是你期望的内容：

![Porter-Duff Source Over](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff Source Over")

源的透明像素允许背景以显示通过，而略带蓝色源像素会掩盖背景。 这是正常情况，在 SkiaSharp 中称为 `SKBlendMode.SrcOver`。 该值是第一次实例化 `SKPaint` 对象时 `BlendMode` 属性的默认设置。

但是，就可以以指定不同的效果的不同的混合模式。 如果指定 "`SKBlendMode.DstOver`"，则在 "源" 和 "目标" 区域中，将显示目标而不是 "源"：

![Porter-Duff Destination](porter-duff-images/PorterDuffDstOver.png "Porter-Duff Destination")

`SKBlendMode.DstIn` blend 模式仅显示目标和源使用目标颜色相交的区域：

![Porter-Duff Destination In](porter-duff-images/PorterDuffDstIn.png "Porter-Duff Destination In")

混合模式 `SKBlendMode.Xor` （异或）会导致在两个区域重叠的位置不出现任何内容：

![Porter-Duff Exclusive 或](porter-duff-images/PorterDuffXor.png "Porter-Duff Exclusive 或")

彩色的目标和源矩形有效地将显示图面划分为四个可以以各种方式对应的目标和源矩形存在的唯一区域：

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter Duff")

右上方和左下角矩形是始终为空，因为在这些领域中的目标和源是透明。 目标颜色占用窗口左上区域中，以便区域或者与目标颜色或根本不是颜色。 同样，源颜色占用右下角区域中，以便与源颜色或根本不是，可以设置颜色区域。 可以与目标颜色，源颜色，或者根本未着色目标和源中间的交集。

组合的总数乘以时间 （适用于中心），3 或 12 2 （对于右下角） 是 2 （对于左上角）。 这些是 12 基本 Porter Duff 组合模式。

在_复合数字图像_（页256）结束时，Porter 和 Duff 会添加一个名为_plus_的第13个模式（对应于 SKIASHARP `SKBlendMode.Plus` 成员和 w3c_浅_模式，这一点不会与 w3c_淡化_模式混淆）。）此 `Plus` 模式添加了目标颜色和源颜色，稍后将对此进行更详细的介绍。

Skia 添加了一个称为 `Modulate` 的14种模式，该模式与 `Plus` 相似，只不过目标颜色和源颜色相乘。 它可将其视为其他 Porter Duff 混合模式。

以下是 14 Porter Duff 模式 SkiaSharp 中定义。 下表显示如何颜色上图中的三个非空白区域的每个：

| “模式”       | 目标 | 交集 | 源 |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | 源       | X      |
| `Dst`      | X           | 目标  |        |
| `SrcOver`  | X           | 源       | X      |
| `DstOver`  | X           | 目标  | X      |
| `SrcIn`    |             | 源       |        |
| `DstIn`    |             | 目标  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | 源       |        |
| `DstATop`  |             | 目标  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | SUM          | X      |
| `Modulate` |             | Products      |        | 

这些混合模式是对称的。 可交换的源和目标，且所有的模式仍可用。

命名约定的模式遵循几个简单规则：

- **Src**或**Dst**本身意味着只有源或目标像素可见。
- **Over**后缀指示交集中的可见内容。 "结束"的其他绘制的源或目标。
- **In**后缀意味着只有交集处于彩色。 输出被限制为只有在源或目标可以是"in"的其他部分。
- **Out**后缀表示没有为交集着色。 输出只是交集的一部分的源或目标可以是交集的"out"。
- 内**后缀是** **In**和**Out**的联合。它包括源或目标为另一位置的 "顶部" 区域。

请注意与 `Plus` 模式和 `Modulate` 模式之间的差异。 这些模式对源和目标像素执行不同类型的计算。 他们稍后所述更多详细信息。

**Porter-Duff 网格**页在一个屏幕上以网格的形式显示所有14个模式。 每个模式都是 `SKCanvasView`的单独实例。 出于此原因，类派生自名为 `PorterDuffCanvasView`的 `SKCanvasView`。 静态构造函数创建另一个使用其左上角区域中的 brownish 矩形，另一个略带蓝色矩形具有相同大小的两个位图：

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

实例构造函数具有类型 `SKBlendMode`的参数。 它将此参数保存在字段中。 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

`OnPaintSurface` 重写绘制两个位图。 通常情况下绘制第一个：

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

第二个用 `SKPaint` 对象绘制，其中 `BlendMode` 属性已设置为构造函数参数：

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

`OnPaintSurface` 重写的剩余部分在位图周围绘制一个矩形，以指示其大小。

`PorterDuffGridPage` 类创建每个 `PorterDurffCanvasView`的14个实例，每个实例对应于 `blendModes` 数组的每个成员。 数组中 `SKBlendModes` 成员的顺序与表之间的不同之处在于将相似的模式彼此相邻。 `PorterDuffCanvasView` 的14个实例与 `Grid`中的标签一起组织：

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

结果如下：

[![Porter-Duff 网格](porter-duff-images/PorterDuffGrid.png "Porter-Duff 网格")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

你将想要使您自己确信透明度是至关重要的 Porter Duff 的混合模式运行正常。 `PorterDuffCanvasView` 类总共包含三次对 `Canvas.Clear` 方法的调用。 所有这些使用无参数方法，它将所有像素都设置为透明：

```csharp
canvas.Clear();
```

请尝试更改这些调用的任何位置，以像素为单位设置为不透明的白色：

```csharp
canvas.Clear(SKColors.White);
```

按照此更改后，一些的混合模式将起来起作用，但其他人将不会。 如果将源位图的背景设置为白色，则 `SrcOver` 模式不起作用，因为在源位图中没有透明像素允许目标显示。 如果将目标位图的背景或画布设置为白色，则 `DstOver` 不起作用，因为目标没有任何透明像素。

可能有一个用来将**Porter-Duff 网格**页中的位图替换为更简单的 `DrawRect` 调用。 有关目标矩形而不是源矩形，这将起作用。 源矩形必须包含不止是颜色色调蓝色的区域。 源矩形必须包含对应于目标的彩色区域的透明区域。 这些仅然后将混合模式下工作。

## <a name="using-mattes-with-porter-duff"></a>使用 Porter Duff 遮罩

"**砖墙合成**" 页显示了一个经典合成任务的示例：需要从多个部分组合图片，包括需要消除背景的位图。 下面是有问题背景的**SeatedMonkey**位图：

![本身的猴子](porter-duff-images/SeatedMonkey.jpg "本身的猴子")

为准备合成，将创建相应的_遮罩_，这是一个黑色的另一个位图，你希望图像显示为黑色，否则为透明。 此文件名为**SeatedMonkeyMatte** ，位于[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**Media**文件夹中的资源内：

![固定的猴子遮罩](porter-duff-images/SeatedMonkeyMatte.png "固定的猴子遮罩")

这并_不_是熟练创建的遮罩。 理想情况下，亚光效果应包括部分透明的黑色像素，边缘像素和此亚光效果却没有。

**砖墙合成**页面的 XAML 文件将实例化一个 `SKCanvasView`，并通过一个 `Button` 来引导用户完成构成最终图像的过程：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载所需的两个位图，并处理 `Button`的 `Clicked` 事件。 对于每个 `Button` 单击 "`step`" 字段会递增，并为 `Button`设置新的 `Text` 属性。 如果 `step` 达到5，则将其设置回0：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

程序首次运行时，不会显示任何内容，`Button`除外：

[![砖墙合成步骤0](porter-duff-images/BrickWallCompositing0.png "砖墙合成步骤0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

按 `Button` 一次会导致 `step` 递增到1，`PaintSurface` 处理程序现在会显示**SeatedMonkey**：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

没有 `SKPaint` 的对象，因此没有混合模式。 位图显示在屏幕的底部：

[![砖墙合成步骤1](porter-duff-images/BrickWallCompositing1.png "砖墙合成步骤1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

再次按下 `Button`，并将 `step` 递增为2。 这是显示**SeatedMonkeyMatte**文件的关键步骤：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

Blend 模式为 `SKBlendMode.DstIn`，这意味着目标将保留在与源的非透明区域相对应的区域中。 对应于原始位图的目标矩形的其余部分将变为透明：

[![砖墙合成步骤2](porter-duff-images/BrickWallCompositing2.png "砖墙合成步骤2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

在后台已删除。 

下一步是要绘制一个矩形，类似于 monkey 坐在人行道。 此人行道的外观取决于两个着色器的组合： 纯色着色器和 Perlin 噪音着色器：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

由于此人行道必须位于猴子后面，因此 `DstOver`blend 模式。 仅在背景色为透明色其中仅显示目标：

[![砖墙合成步骤3](porter-duff-images/BrickWallCompositing3.png "砖墙合成步骤3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

最后一步添加产生效果。 该程序使用作为 `AlgorithmicBrickWallPage` 类中 `BrickWallTile` 的静态属性的程序块墙位图磁贴。 将转换转换添加到 `SKShader.CreateBitmap` 调用，以移动磁贴，使底部的行成为完整磁贴：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

为方便起见，`DrawRect` 调用在整个画布上显示此着色器，但是 `DstOver` 模式将输出限制为仅在画布上保持透明的区域：

[![砖墙合成步骤4](porter-duff-images/BrickWallCompositing4.png "砖墙合成步骤4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

显然，还有其他方式来编写此场景。 它可以开始在后台和前台进行构建。 但使用的混合模式提供了更大的灵活性。 具体而言，遮罩的使用可以从组合场景要排除的位图的背景。

正如您在[利用路径和区域进行剪辑](../../curves/clipping.md)中学到的，`SKCanvas` 类定义了三种类型的剪辑，它们对应于[`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*)、 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*)和[`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*)方法。 Porter Duff 的混合模式添加另一种类型的剪辑，允许限制对任何内容，您可以绘制，包括位图图像。 **砖墙组合**中使用的遮罩实质上定义了剪辑区域。

## <a name="gradient-transparency-and-transitions"></a>渐变透明度和转换

Porter Duff 示例混合模式在这篇文章有所有涉及组成不透明像素和透明的像素为单位，但不是部分透明的像素的图像前面所示。 混合模式函数为这些像素为单位定义。 下表是 Porter-Duff 混合模式的更正式定义，该模式使用在 Skia [**SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference)中找到的表示法。 （由于**SkBlendMode 引用**是 Skia 引用， C++因此使用语法。）

从概念上讲，每个像素的红色、 绿色、 蓝色和 alpha 组件从字节转换为 0 到 1 范围内的浮点数。 Alpha 通道，0 表示完全透明，1 是完全不透明

下表中的表示法使用下列缩写：

- **Da**是目标 alpha 通道
- **Dc**是目标 RGB 颜色
- **Sa**是源 alpha 通道
- **Sc**是源 RGB 颜色

RGB 颜色预先乘以 alpha 值。 例如，如果**Sc**表示纯红色但**Sa**为0x80，则 RGB 颜色为 **（0x80，0，0）** 。 如果**Sa**为0，则所有 RGB 组件也都为零。

结果显示在带 alpha 通道的方括号中，RGB 颜色用逗号分隔： **[alpha，color]** 。 颜色为红色、 绿色和蓝色组件单独执行计算：

| “模式”       | Operation |
| ---------- | --------- |
| `Clear`    | [0，0]    |
| `Src`      | [Sa，Sc]  |
| `Dst`      | [Da，Dc]  |
| `SrcOver`  | [Sa + Da·(1 – Sa)，Sc + Dc·(1 – Sa) | 
| `DstOver`  | [Da + Sa·(1 – Da) Dc + Sc·(1 – Da) |
| `SrcIn`    | [Sa·Da Sc·Da] |
| `DstIn`    | [Da·Sa Dc·Sa] |
| `SrcOut`   | [Sa·(1 – Da) Sc·(1 – Da)] |
| `DstOut`   | [Da·(1 – Sa)，Dc·(1 – Sa)] |
| `SrcATop`  | [Da Sc·Da + Dc·(1 – Sa)] |
| `DstATop`  | [Sa Dc·Sa + Sc·(1 – Da)] |
| `Xor`      | [Sa + Da – 2·Sa·Da Sc·(1 – Da) + Dc·(1 – Sa)] |
| `Plus`     | [Sa + Da、 Sc + Dc] |
| `Modulate` | [Sa·Da Sc·Dc] | 

当**Da**和**Sa**为0或1时，可以更轻松地分析这些操作。 例如，对于默认 `SrcOver` 模式，如果**Sa**为0，则**Sc**也是0，结果为 **[Da，Dc]** ，目标 alpha 和颜色。 如果**Sa**为1，则结果为 **[Sa，Sc]** ，源 alpha 和颜色，或者 **[1，Sc]** 。

"`Plus`" 和 "`Modulate`" 模式与其他模式略有不同，因为这可能会导致源和目标的组合产生新的颜色。 `Plus` 模式可以用字节组件或浮点组件来解释。 在前面所示的**Porter-Duff 网格**页中，目标颜色为 **（0xC0，0x80，0x00）** ，而源颜色为 **（0x00，0x80，0xC0）** 。 添加组件的每个对，但在 0xFF 其限制之和。 结果是颜色 **（0xC0，0xff，0xC0）** 。 这是交集所示的颜色。

对于 `Modulate` 模式，RGB 值必须转换为浮点值。 目标颜色为 **（0.75，0.5，0）** ，源为 **（0，0.5，0.75）** 。 RGB 组件分别相乘，结果为 **（0，0.25，0）** 。 这是此模式的**Porter-Duff 网格**页中的交集显示的颜色。

通过**Porter-Duff 透明度**页，可以检查 Porter-Duff blend 模式对部分透明的图形对象的操作方式。 XAML 文件包括 Porter-Duff 模式的 `Picker`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

代码隐藏文件已满使用线性渐变的大小相同的两个矩形。 目标渐变是从右上到左下。 它是 brownish 右上角中，但然后向中心开始淡入淡出为透明，并是透明的左下角。 

源矩形具有从左上向右下的渐变。 左上角是略带蓝色但再次淡出为透明的并且是透明的右下角。 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

此程序演示 Porter Duff 的混合模式可用于非位图图形对象。 但是，源必须包括透明区域。 这是此处的示例，因为渐变填充矩形，但的渐变的部分是透明。

以下是三个示例：

[![Porter-Duff 透明度](porter-duff-images/PorterDuffTransparency.png "Porter-Duff 透明度")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

目标和源的配置与原始 Porter-Duff[_合成数字图像_](https://graphics.pixar.com/library/Compositing/paper.pdf)纸张的第255页中显示的关系图非常相似，但此页说明混合模式对于部分透明度的区域而言是良好的。

对于一些不同的效果，可以使用透明的梯度。 一种可能是屏蔽，这类似于 " **SkiaSharp 循环渐变" 页**的 "[**遮蔽渐变**](../shaders/circular-gradients.md#radial-gradients-for-masking)" 部分中显示的方法。 很多**组合掩码**页与上述程序类似。 它加载位图资源，并确定要在其中显示该矩形。 径向渐变是基于创建的预先确定的中心和半径：

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

此程序的不同之处是渐变中心的黑色开头和结尾的透明度。 它显示在混合模式为 "`DstIn`" 的位图上，它仅在不透明的源区域中显示目标。

`DrawRect` 调用后，画布的整个图面都是透明的，但径向渐变定义的圆圈除外。 由最后一次调用：

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

被粉色的画布的透明区域：

[![合成掩码](porter-duff-images/CompositingMask.png "合成掩码")](porter-duff-images/CompositingMask-Large.png#lightbox)

此外可以使用 Porter Duff 模式和部分透明渐变到另一个图像中的转换。 "**渐变转换**" 页包含一个 `Slider`，以指示从0到1的转换进度级别，并使用 `Picker` 来选择所需的转换类型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载两个位图资源，以演示转换。 本文前面的**位图溶解**页中使用的两个图像相同。 该代码还定义了一个枚举，其中三个成员对应于三种类型的渐变 &mdash; 线性、径向和扫描。 这些值将加载到 `Picker`中：

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

代码隐藏文件创建三个 `SKPaint` 对象。 `paint0` 对象不使用混合模式。 此画图对象用于绘制一个矩形，该矩形具有从黑色到透明的渐变，如 `colors` 数组中所示。 `positions` 数组基于 `Slider`的位置，但在某种程度上进行了调整。 如果 `Slider` 最小值或最大值，则 `progress` 值为0或1，两个位图中的一个应完全可见。 必须对这些值相应地设置 `positions` 数组。

如果 `progress` 值为0，则 `positions` 数组包含值-0.1 和0。 SkiaSharp 将否则调整将等于 0，表示渐变是黑色只能在 0 和透明第一个值。 如果 `progress` 为0.5，则数组包含值0.45 和0.55。 渐变是黑色从 0 到 0.45，则将转换为透明的并且是完全透明的 0.55 为 1。 如果 `progress` 为1，则 `positions` 数组为1和1.1，这意味着渐变是从0到1的黑色。

`colors` 和 `position` 数组均用于创建渐变的 `SKShader` 的三个方法。 这三个着色器中只会根据 `Picker` 选择创建一个着色器： 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

但不使用混合模式的矩形中显示该渐变。 `DrawRect` 调用后，画布只包含从黑色到透明的渐变。 具有更高 `Slider` 值的黑色增加量。

在 `PaintSurface` 处理程序的最后四个语句中，将显示两个位图。 `SrcOut` blend 模式意味着第一个位图只能显示在背景的透明区域中。 第二个位图的 `DstOver` 模式表示第二个位图仅显示在第一个位图未显示的区域中。

以下屏幕截图显示三个不同的转换类型，每 50%标记处：

[![梯度转换](porter-duff-images/GradientTransitions.png "梯度转换")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
