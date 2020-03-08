---
title: 显示 SkiaSharp 位图
description: 了解如何显示的 SkiaSharp 中像素的位图大小和扩展以填充矩形，同时保存纵横比。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 9955b68346c74435a3a141c69d02e1bec5856bd3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915998"
---
# <a name="displaying-skiasharp-bitmaps"></a>显示 SkiaSharp 位图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp 位图的主题在 SkiaSharp 中的 **[位图基础知识](../basics/bitmaps.md)** 一文中引入。 该文章介绍了三种方式来加载位图并通过三种方式显示位图。 本文介绍加载位图并更深入地使用 `SKCanvas`的 `DrawBitmap` 方法的方法。

![显示示例](displaying-images/DisplayingSample.png "显示示例")

**[SkiaSharp 位图的分段显示](segmented.md)** 文章中讨论了 `DrawBitmapLattice` 和 `DrawBitmapNinePatch` 方法。

本页中的示例来自 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序。 在该应用程序的主页中，选择 " **SkiaSharp 位图**"，然后切换到 "**显示位图**" 部分。

## <a name="loading-a-bitmap"></a>正在加载位图

SkiaSharp 应用程序通常使用的位图来自三个不同来源之一：

- 借助 Internet 多
- 从可执行文件中嵌入的资源
- 从用户的照片库

还有可能 SkiaSharp 应用程序以创建新的位图，然后在其上绘制或从算法上设置位图位。 这些技术在 **[SkiaSharp 位图上的 "创建和绘制](drawing.md)** " 和 " **[访问 SkiaSharp 位图像素](pixel-bits.md)** " 文章中进行了介绍。

在以下三个用于加载位图的代码示例中，类被假定为包含类型 `SKBitmap`的字段：

```csharp
SKBitmap bitmap;
```

如 SkiaSharp 中所述的 " **[位图基础](../basics/bitmaps.md)** " 这一文章中所述，通过 Internet 加载位图的最佳方式是使用[`HttpClient`](xref:System.Net.Http.HttpClient)类。 类的单个实例可以定义为字段：

```csharp
HttpClient httpClient = new HttpClient();
```

使用 iOS 和 Android 应用程序 `HttpClient` 时，需要按照 **[传输层安全性（TLS） 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 文档中所述设置项目属性。

使用 `HttpClient` 的代码通常涉及 `await` 运算符，因此它必须位于 `async` 方法中：

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

请注意，从 `GetStreamAsync` 获取的 `Stream` 对象将被复制到 `MemoryStream`中。 Android 不允许主线程处理 `Stream` `HttpClient`，但异步方法除外。 

[`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream))执行大量工作：传递给它的 `Stream` 对象引用一个内存块，其中包含一个通用位图文件格式（通常为 JPEG、PNG 或 GIF）中的整个位图。 `Decode` 方法必须确定格式，然后将位图文件解码为 SkiaSharp 自己的内部位图格式。

在代码调用 `SKBitmap.Decode`后，它可能会使 `CanvasView` 失效，以便 `PaintSurface` 处理程序可以显示新加载的位图。

第二种方法来加载位图是通过.NET Standard 库中嵌入的资源中包括位图引用单个平台项目。 资源 ID 被传递到[`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String))方法。 此资源 ID 组成的程序集名称、 文件夹名称和文件名用句点分隔的资源：

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

也可以为 iOS、 Android 和通用 Windows 平台 (UWP) 的单个平台项目中的资源存储位图文件。 但是，加载这些位图需要位于平台项目中的代码。

获取位图的第三个方法是从用户的图片库。 以下代码使用 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序中包含的依赖项服务。 **SkiaSharpFormsDemo** .NET Standard 库包含 `IPhotoLibrary` 接口，而每个平台项目都包含一个实现该接口的 `PhotoLibrary` 类。

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

通常，此类代码还会使 `CanvasView` 失效，使 `PaintSurface` 处理程序可以显示新的位图。

`SKBitmap` 类定义多个有用的属性，其中包括[`Width`](xref:SkiaSharp.SKBitmap.Width)和[`Height`](xref:SkiaSharp.SKBitmap.Height)，它们显示位图的像素尺寸以及许多方法，其中包括用于创建和复制位图、用于公开像素位的方法。 

## <a name="displaying-in-pixel-dimensions"></a>在像素尺寸中显示

SkiaSharp [`Canvas`](xref:SkiaSharp.SKCanvas)类定义四个 `DrawBitmap` 方法。 这些方法允许位图，以显示两个完全不同的方式： 

- 指定 `SKPoint` 值（或单独的 `x` 和 `y` 值）将以像素尺寸显示位图。 位图的像素是直接映射到的视频显示器的像素为单位。
- 指定矩形将导致要被拉伸到的大小和形状的矩形的位图。 

使用带有 `SKPoint` 参数的[`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) ，或具有单独 `x` 和 `y` 参数的[`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) ，在其像素维度中显示位图：

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

这两种方法是在功能上相同的。 指定的点指示相对于 canvas 位图左上角的位置。 由于移动设备的像素的分辨率很高，较小位图通常显示在这些设备上很小。

可选 `SKPaint` 参数允许使用透明度显示位图。 为此，请创建一个 `SKPaint` 对象，并将 `Color` 属性设置为 alpha 通道小于1的任何 `SKColor` 值。 例如：

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

作为最后一个参数传递 0x80 指示 50%的透明度。 此外可以在其中一个预定义的颜色设置 alpha 通道：

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

但是，是颜色本身是不相关。 使用 `DrawBitmap` 调用中的 `SKPaint` 对象时，仅检查 alpha 通道。

当使用 blend 模式或滤镜效果显示位图时，`SKPaint` 对象也会扮演角色。 这些文章在[SkiaSharp 合成和 blend 模式](../effects/blend-modes/index.md)和[SkiaSharp 映像筛选器](../effects/image-filters.md)中进行了演示。

**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 示例程序中的 "**像素维度**" 页显示的位图资源宽度为320像素，高度为240像素：

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

`PaintSurface` 处理程序通过基于显示图面的像素尺寸和位图的像素尺寸计算 `x` 和 `y` 值来使位图居中：

[![像素尺寸](displaying-images/PixelDimensions.png "像素尺寸")](displaying-images/PixelDimensions-Large.png#lightbox)

如果应用程序想要在其左上角显示位图，它将只需传递的坐标 （0，0）。 

## <a name="a-method-for-loading-resource-bitmaps"></a>用于加载资源位图方法

有很多即将推出的示例将需要加载位图资源。 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 解决方案中的静态 `BitmapExtensions` 类包含方法来帮助你：

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

请注意 `Type` 参数。 这可以是与存储位图资源的程序集中的任何类型相关联的 `Type` 对象。

此 `LoadBitmapResource` 方法将用于需要位图资源的所有后续示例中。

## <a name="stretching-to-fill-a-rectangle"></a>拉伸以填充矩形

`SKCanvas` 类还定义了一个[`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint))方法，该方法将位图呈现为一个矩形，另一个[`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint))方法将位图的矩形子集呈现为一个矩形：

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

在这两种情况下，都会拉伸位图以填充名为 `dest`的矩形。 在第二个方法中，`source` 矩形允许您选择位图的子集。 `dest` 矩形是相对于输出设备的;`source` 矩形相对于位图。

"**填充矩形**" 页通过在与画布相同大小的矩形中显示与前面示例中所用相同的位图来演示这两种方法中的第一个。 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

请注意，使用 new `BitmapExtensions.LoadBitmapResource` 方法设置 `SKBitmap` 字段。 目标矩形是从 `SKImageInfo`的[`Rect`](xref:SkiaSharp.SKImageInfo.Rect)属性获取的，该属性介绍显示图面的大小：

[![填充矩形](displaying-images/FillRectangle.png "填充矩形")](displaying-images/FillRectangle-Large.png#lightbox)

这通常_不_是你想要的。 图像是因在水平和垂直方向上以不同的方式拉伸而失真。 当非像素大小显示位图，通常您想要保留位图的原始纵横比。

## <a name="stretching-while-preserving-the-aspect-ratio"></a>拉伸同时保存纵横比

在保持纵横比的同时拉伸位图也称为_统一缩放_。 该术语建议算法的方法。 **统一缩放**页面中显示了一个可能的解决方案：

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

`PaintSurface` 处理程序将计算一个 `scale` 系数，它是显示宽度和高度与位图宽度和高度之比的最小值。 然后，可以计算 `x` 和 `y` 值，以便在显示宽度和高度范围内居中缩放位图。 目标矩形的左上角为 `x`，`y` 并在这些值的右下角加上位图的缩放宽度和高度：

[![统一缩放](displaying-images/UniformScaling.png "统一缩放")](displaying-images/UniformScaling-Large.png#lightbox)

打开手机，请参阅已延伸到该区域的位图：

[![均匀缩放横向](displaying-images/UniformScaling-Landscape.png "均匀缩放横向")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

如果要实现略微不同的算法，使用此 `scale` 因素的优点会很明显。 假设你想要保留位图的长宽比，但还填充目标矩形。 这种方法的唯一方法是通过裁剪部分图像来实现，但你只需通过将 `Math.Min` 更改为在上面的代码中 `Math.Max` 来实现该算法。 结果如下： 

[![统一缩放替代方法](displaying-images/UniformScaling-Alternative.png "统一缩放替代方法")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

位图的纵横比会保留，但有所剪裁区域左侧和右侧的位图。

## <a name="a-versatile-bitmap-display-function"></a>通用位图显示函数

基于 XAML 的编程环境 （例如 UWP 和 Xamarin.Forms） 具有一个工具用于扩展或压缩位图的大小，同时保留其纵横比。 虽然 SkiaSharp 不包括此功能，可以自行实现。 [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序中包含的 `BitmapExtensions` 类演示了如何操作。 类定义了两个执行纵横比计算的新 `DrawBitmap` 方法。 这些新方法是 `SKCanvas`的扩展方法。

新的 `DrawBitmap` 方法包含一个 `BitmapStretch`类型的参数，该参数是在**BitmapExtensions.cs**文件中定义的枚举：

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

`None`、`Fill`、`Uniform`和 `UniformToFill` 成员与 UWP [`Stretch`](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx)枚举中的成员相同。 类似的 Xamarin. [`Aspect`](xref:Xamarin.Forms.Aspect)枚举定义成员 `Fill`、`AspectFit`和 `AspectFill`。

上面所示的 "**统一缩放**" 页将位图嵌入到矩形中，但你可能需要其他选项，例如将位图放置在矩形的左侧或右侧，或者顶部或底部。 这就是 `BitmapAlignment` 枚举的目的：

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

与 `BitmapStretch.Fill`一起使用时，对齐设置不起作用。

第一个 `DrawBitmap` 扩展函数包含一个目标矩形，但没有源矩形。 定义了默认值，因此，如果想要将位图居中，只需指定 `BitmapStretch` 成员即可：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

此方法的主要目的是计算名为 `scale` 的缩放系数，然后在调用 `CalculateDisplayRect` 方法时将其应用到位图宽度和高度。 这是计算矩形用于显示基于水平和垂直对齐的位图的方法：

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

`BitmapExtensions` 类包含一个附加的 `DrawBitmap` 方法，其中包含用于指定位图子集的源矩形。 此方法类似于第一个方法，只不过缩放因子是根据 `source` 矩形计算的，然后应用于对 `CalculateDisplayRect`的调用中的 `source` 矩形：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

这两个新 `DrawBitmap` 方法中的第一种在**缩放模式**页中进行演示。 XAML 文件包含三个 `Picker` 元素，这些元素使您可以选择 `BitmapStretch` 的成员和 `BitmapAlignment` 枚举：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

当任何 `Picker` 项发生更改时，代码隐藏文件只会使 `CanvasView` 无效。 `PaintSurface` 处理程序访问三个用于调用 `DrawBitmap` 扩展方法的 `Picker` 视图：

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

下面是一些组合的选项：

[![缩放模式](displaying-images/ScalingModes.png "缩放模式")](displaying-images/ScalingModes-Large.png#lightbox)

**矩形子集**页与**缩放模式**几乎具有相同的 XAML 文件，但代码隐藏文件定义了由 `SOURCE` 字段指定的位图的矩形子集： 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

此矩形源隔离 monkey 的头，如以下屏幕截图中所示：

[![矩形子集](displaying-images/RectangleSubset.png "矩形子集")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
