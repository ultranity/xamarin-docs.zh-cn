---
title: SkiaSharp 位图进行动画处理
description: 了解如何： 按顺序显示一系列的位图，并呈现动画的 GIF 文件执行位图动画。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 33e17a01d8a13fcdaee27e5857c554a4a232c534
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917891"
---
# <a name="animating-skiasharp-bitmaps"></a>SkiaSharp 位图进行动画处理

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

对 SkiaSharp 图形进行动画处理的应用程序通常会按固定速率（通常每16毫秒）调用 `SKCanvasView` 上的 `InvalidateSurface`。 使图面失效将触发对 `PaintSurface` 处理程序的调用以重绘显示。 当每秒 60 次重新绘制视觉对象，它们显示以顺利进行动画处理。

但是，如果图形过于复杂，需要呈现在 16 毫秒，动画会抖动。 程序员可能会选择刷新频率减少到 30 倍或 15 次第二个，但有时甚至这是不足够。 有时图形很复杂，它们只是无法呈现实时。

一种解决方案是动画的通过呈现位图的一系列上的各个帧事先做好动画。 若要显示动画，它只是用来按顺序显示这些位图，则会每秒 60 次。

当然，这可能是大量的位图，但这就是如何大预算 3D 动画的影视进行。 3D 图形是得太复杂，无法在真实时间中呈现。 需要大量处理时间来呈现每个帧。 观看电影时看到的内容是位图的实质上是位图的一系列。

您可以执行类似 SkiaSharp 中的操作。 本文演示了两种类型的位图动画。 第一个示例是一个动画的 Mandelbrot 集：

![动画采样](animating-images/AnimatingSample.png "动画采样")

第二个示例演示如何使用 SkiaSharp 呈现动画的 GIF 文件。

## <a name="bitmap-animation"></a>位图动画

Mandelbrot 集是以可视方式有吸引力，但 computionally 耗时较长。 （有关在此处使用的 Mandelbrot 集和数学的讨论，请参阅从第666页开始[_创建具有 Xamarin 的移动应用_的第20章。](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) 以下说明假定该背景知识）。

[**Mandelbrot 动画**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)示例使用位图动画来模拟 Mandelbrot 集中固定点的连续缩放。 放大后跟缩小，，然后重复这一循环下去，或者直到结束程序。

通过创建最多 50 位图，它在应用程序本地存储中存储此动画准备程序。 每个位图包含一半的宽度和高度的复平面与以前的位图。 （在程序中，这些位图称为表示整数_缩放级别_。）然后按顺序显示位图。 提供从一个位图的平滑过渡到另一个的动画的每个位图缩放。

与_使用 Xamarin 创建移动应用_一章中所述的最后一个程序一样， **Mandelbrot 动画**中 Mandelbrot 集的计算是一个具有八个参数的异步方法。 参数包括复杂的中心点，以及宽度和高度复平面围绕该中心点。 接下来三个参数是像素宽度和要创建位图的高度和最大递归计算的迭代数。 `progress` 参数用于显示此计算的进度。 此程序中未使用 `cancelToken` 参数：

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

方法返回 `BitmapInfo` 类型的对象，该对象提供用于创建位图的信息：

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

**Mandelbrot 动画**XAML 文件包括两个 `Label` 视图、`ProgressBar`和 `Button` 以及 `SKCanvasView`：

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

代码隐藏文件开头定义三个重要的常量和位图的数组：

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

在某些时候，你可能需要将 `COUNT` 值更改为50，以查看动画的全部范围。 大于 50 的值不是很有用。 围绕 48 左右的缩放级别，双精度浮点数中的解决方法就无法满足的 Mandelbrot 集计算。 _创建具有 Xamarin 的移动应用_的684页讨论了此问题。

`center` 值非常重要。 这是动画缩放的焦点。 文件中的三个值是在第20章使用_Xamarin 创建移动应用_程序的第20个屏幕截图中所使用的值。窗体，但你可以在684第一章中尝试使用一个你自己的值。

**Mandelbrot 动画**示例将这些 `COUNT` 位图存储在本地应用程序存储中。 50 个位图需要超过 20 兆字节的存储在设备上，因此你可能想要知道多少存储空间占用这些位图，并在某些时候，可能想要删除所有这些。 这就是 `MainPage` 类底部的两种方法的用途：

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

因为该程序将它们保留在内存中，该程序动画处理这些相同的位图时，可以删除本地存储区中的位图。 但在下次运行该程序，它将需要重新创建位图。

存储在本地应用程序存储中的位图将 `center` 值纳入其文件名中，因此，如果更改 `center` 设置，则不会在存储中替换现有的位图，并将继续占用空间。

下面是 `MainPage` 用来构造文件名的方法，以及用于根据颜色组件定义像素值的 `MakePixel` 方法：

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

要 `FilePath` 的 `zoomLevel` 参数的范围为0到 `COUNT` 常量减1。

`MainPage` 构造函数调用 `LoadAndStartAnimation` 方法：

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

`LoadAndStartAnimation` 方法负责访问应用程序本地存储，以便加载在以前运行程序时可能创建的任何位图。 它循环遍历0到 `COUNT``zoomLevel` 值。 如果该文件存在，它会将其加载到 `bitmaps` 数组。 否则，需要通过调用 `Mandelbrot.CalculateAsync`为特定的 `center` 和 `zoomLevel` 值创建位图。 该方法获取每个像素，此方法将颜色转换为迭代计数：

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

请注意，该程序将存储这些位图中本地应用程序存储，而不是设备的照片库中。 .NET Standard 2.0 库允许为此任务使用熟悉的 `File.OpenRead` 和 `File.WriteAllBytes` 方法。

创建所有位图或将其加载到内存中后，该方法将启动一个 `Stopwatch` 对象并调用 `Device.StartTimer`。 每16毫秒调用一次 `OnTimerTick` 方法。

`OnTimerTick` 计算的 `time` 值（以毫秒为单位），范围为从0到6000次 `COUNT`，这 apportions 6 秒，用于显示每个位图。 `progress` 值使用 `Math.Sin` 值来创建正弦动画，该动画在循环开始时速度较慢，结束时速度较慢。

`progress` 值的范围为0到 `COUNT`。 这意味着 `progress` 的整数部分是 `bitmaps` 数组的索引，而 `progress` 的小数部分则表示该特定位图的缩放级别。 这些值存储在 "`bitmapIndex`" 和 "`bitmapProgress`" 字段中，由 XAML 文件中的 `Label` 和 `Slider` 显示。 `SKCanvasView` 失效，因此无法更新位图显示：

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

最后，`SKCanvasView` 的 `PaintSurface` 处理程序计算一个目标矩形，以便在保持纵横比的同时尽可能大地显示位图。 源矩形基于 `bitmapProgress` 值。 当 `bitmapProgress` 为0时，此处计算的 `fraction` 值范围从0到显示整个位图，0.25 时为，当 `bitmapProgress` 为1时，显示位图的宽度和高度的一半，有效地放大：

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

下面是正在运行的程序：

[![Mandelbrot 动画](animating-images/MandelbrotAnimation.png "Mandelbrot 动画")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>动画 GIF

图形交换格式 (GIF) 规范包括一个功能，使单个的 GIF 文件可以包含可连续，通常在循环中显示场景的多个连续帧。 这些文件称为_动画 gif_。 Web 浏览器可以播放动画的 Gif 和 SkiaSharp 允许应用程序从动画 GIF 文件中提取的帧并按顺序显示它们。

[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例包含一个名为**Newtons_cradle_animation_book_2 .GIF**的动画 gif 资源，该资源由 DemonDeLuxe 创建并从维基百科的[牛顿底座](https://en.wikipedia.org/wiki/Newton%27s_cradle)页面下载。 **动画 GIF**页面包含一个 XAML 文件，该文件提供该信息并实例化一个 `SKCanvasView`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

代码隐藏文件未通用化播放任何动画的 GIF 文件。 它会忽略的一些信息不可用，具体而言，一个重复计数，且只需播放在循环中的动态 gif 文件。

SkisSharp 提取的帧的动画 GIF 文件使用似乎不记录任何位置，因此下面的代码的说明是比平常更多详细：

动画 GIF 文件解码出现在页面的构造函数中，要求使用引用位图的 `Stream` 对象创建 `SKManagedStream` 对象，然后使用[`SKCodec`](xref:SkiaSharp.SKCodec)对象。 [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount)属性指示构成动画的帧数。

这些帧最终将另存为单独的位图，因此构造函数使用 `FrameCount` 分配 `SKBitmap` 类型的数组，并在每个帧的持续时间内分配两个 `int` 数组，并将累积的持续时间（以简化动画逻辑）。

`SKCodec` 类的[`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo)属性是[`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo)值的数组，每个帧对应一个值，但是此程序采用的唯一内容是帧的[`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) ，以毫秒为单位。

`SKCodec` 定义[`SKImageInfo`](xref:SkiaSharp.SKImageInfo)类型的名为[`Info`](xref:SkiaSharp.SKCodec.Info)的属性，但 `SKImageInfo` 值指示该颜色类型 `SKColorType.Index8`（至少为此图像），这意味着每个像素是颜色类型的索引。 为了避免麻烦使用颜色表，该程序使用[`Width`](xref:SkiaSharp.SKImageInfo.Width) ，并[`Height`](xref:SkiaSharp.SKImageInfo.Height)该结构中的信息来构造自己的全颜色 `ImageInfo` 值。 每个 `SKBitmap` 都是从创建的。

`SKBitmap` 的 `GetPixels` 方法返回引用该位图的像素位的 `IntPtr`。 尚未设置这些像素位。 该 `IntPtr` 传递到 `SKCodec`的[`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions))方法之一。 该方法将帧从 GIF 文件复制到 `IntPtr`引用的内存空间中。 [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions)构造函数指示帧数：

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

尽管 `IntPtr` 值，但不需要 `unsafe` 代码，因为 `IntPtr` 决不会转换为C#指针值。

在提取每个帧后，构造函数注册的所有帧，持续时间进行合计，然后初始化累计持续时间的另一个数组。

代码隐藏文件的其余部分专用于动画。 `Device.StartTimer` 方法用于启动计时器，`OnTimerTick` 回调使用 `Stopwatch` 对象确定运行时间（以毫秒为单位）。 循环遍历的累计持续时间数组就足以查找当前帧：

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
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
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

每次 `currentframe` 变量发生变化时，`SKCanvasView` 会失效，并显示新帧：

[![动画 GIF](animating-images/AnimatedGif.png "动画 GIF")](animating-images/AnimatedGif-Large.png#lightbox)

当然，你将想要运行该程序自己，查看动画。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Mandelbrot 动画（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
