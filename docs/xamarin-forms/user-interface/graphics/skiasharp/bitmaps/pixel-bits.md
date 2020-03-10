---
title: 访问 SkiaSharp 位图像素位
description: 了解用于访问和修改 SkiaSharp 位图的像素位的各种技术。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918220"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>访问 SkiaSharp 位图像素位

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

正如你在将[**SkiaSharp 位图保存到文件**](saving.md)这篇文章中所看到的那样，位图通常以压缩格式存储在文件中，例如 JPEG 或 PNG。 在与此相反，不压缩 SkiaSharp 位图存储在内存中。 已存储为一系列连续的像素为单位。 此压缩的格式便于将位图转移到显示图面。

SkiaSharp 位图占用的内存块的组织结构非常简单的方式： 它以像素为单位，从左到右，第一行开始，然后继续第二行。 对于全彩色位图，每个像素由四个字节，这意味着位图所需的总内存空间是四次其宽度和高度的产品。

本指南介绍了如何应用程序有权访问这些像素，通过访问位图的像素的内存块，直接或间接。 在某些情况下，程序可能想要分析的图像的像素为单位，并构造某种类型的直方图。 更常见的是，应用程序可以通过从算法上创建构成位图像素构造唯一映像：

![像素位示例](pixel-bits-images/PixelBitsSample.png "像素位示例")

## <a name="the-techniques"></a>技术

SkiaSharp 提供几种方法用于访问位图的像素位。 你选择哪一个通常是编码 （这与维护和易用性调试相关） 的方便和性能权衡。 在大多数情况下，你将使用 `SKBitmap` 的以下方法和属性之一来访问位图的像素：

- `GetPixel` 和 `SetPixel` 方法允许您获取或设置单个像素的颜色。
- `Pixels` 属性获取整个位图的像素颜色数组，或设置颜色的数组。
- `GetPixels` 返回位图使用的像素内存的地址。
- `SetPixels` 替换位图使用的像素内存的地址。

您可以将为"高级别"的前两个技术和第二个两个作为"低级别。 有一些其他方法和属性，您可以使用，但这些是最有价值。

为了使你能够查看这些技术之间的性能差异， [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序包含一个名为 "**渐变位图**" 的页面，它创建一个位图，该位图的像素结合了红色和蓝色底纹来创建渐变。 程序创建八个不同副本，此位图，所有使用的不同的方法用于设置位图像素。 每个这些八个位图创建在单独的方法，还设置该技术的简短文本说明，并计算所需设置所有像素为单位的时间。 每个方法循环访问的像素设置逻辑 100 次若要更好地估计的性能。

### <a name="the-setpixel-method"></a>SetPixel 方法

如果只需设置或获取几个单个像素，则[`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor))和[`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32))方法非常理想。 对于每个这两种方法，您指定的整数列和行。 无论使用何种像素格式，这两种方法都可让你获取或设置作为 `SKColor` 值的像素：

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

`col` 参数的范围必须介于0到位图的 `Width` 属性之间，并且 `row` 范围为0到小于 `Height` 属性。

下面是**渐变位图**中的方法，该方法使用 `SetPixel` 方法设置位图的内容。 位图为 256 x 256 像素，`for` 循环使用值的范围进行硬编码：

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

设置每个像素的颜色具有的红色组件等于位图列和行的蓝色组件等于。 在左上角，红色在右上角，在左下角和右下角，使用其他位置渐变在洋红色蓝黑色合成的位图。

`SetPixel` 方法被称为65536次，无论此方法的效率如何，通常不是很好的做法是，如果有替代方法，则很可能会调用它。 幸运的是，有多种替代方法。

### <a name="the-pixels-property"></a>像素属性

`SKBitmap` 定义一个[`Pixels`](xref:SkiaSharp.SKBitmap.Pixels)属性，该属性返回整个位图的 `SKColor` 值的数组。 你还可以使用 `Pixels` 来设置位图的颜色值数组：

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

像素排列开始的数组中的第一行中，从左到右，然后第二行，等等。 数组中的总数等于位图宽度和高度的产品。

尽管此属性看起来很有效，但请记住，像素是从位图复制到数组中，并从数组重新转换为位图，然后将像素转换为 `SKColor` 值。

下面是使用 `Pixels` 属性设置位图 `GradientBitmapPage` 类中的方法。 方法分配一个所需大小的 `SKColor` 数组，但它可能已使用 `Pixels` 属性创建该数组：

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

请注意，需要从 `row` 和 `col` 变量计算 `pixels` 数组的索引。 行乘以每个行 (在此情况下的 256) 中的像素数，然后添加列。

`SKBitmap` 还定义了类似的 `Bytes` 属性，该属性将为整个位图返回一个字节数组，但对于全颜色位图更繁琐。

### <a name="the-getpixels-pointer"></a>GetPixels 指针

很可能是[`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels)访问位图像素的最强大方法，而不是将其与 `GetPixel` 方法或 `Pixels` 属性混淆。 您会立即注意到 `GetPixels` 的不同之处在于，它会返回编程中C#不太常见的内容：

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [`IntPtr`](xref:System.IntPtr)类型表示指针。 它被称为 `IntPtr`，因为它是运行程序的计算机的本机处理器上的整数长度，通常为32位或64位。 `GetPixels` 返回的 `IntPtr` 是位图对象用于存储其像素的实际内存块的地址。

您可以使用[`ToPointer`](xref:System.IntPtr.ToPointer)方法将 `IntPtr` C#转换为指针类型。 C# 指针语法是 C 和 c + + 相同：

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr` 变量的类型为_byte 指针_。 此 `ptr` 变量使你能够访问用于存储位图像素的单个内存字节。 使用类似下面的代码从此内存中读取一个字节或写入一个字节的内存：

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

在此上下文中，星号为C# _间接寻址运算符_并用于引用 `ptr`指向的内存内容。 最初，`ptr` 指向位图第一行第一个像素的第一个字节，但您可以对 `ptr` 变量执行算术运算，以将其移动到位图中的其他位置。

一个缺点是只能在用 `unsafe` 关键字标记的代码块中使用此 `ptr` 变量。 此外，该程序集必须标记为允许不安全的块。 这是在项目的属性。

在 C# 中使用指针是功能非常强大，但也非常危险。 需要小心，不要访问超出指针是要引用的内存。 这就是原因指针使用程序关联以单词"不安全。"

下面是使用 `GetPixels` 方法的 `GradientBitmapPage` 类中的方法。 请注意包含使用字节指针的所有代码的 `unsafe` 块：

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

第一次从 `ToPointer` 方法获取 `ptr` 变量时，它指向位图第一行最左侧像素的第一个字节。 设置 `row` 和 `col` 的 `for` 循环，以便设置每个像素的每个字节后，`ptr` 可以与 `++` 运算符递增。 对于其他99循环，必须将 `ptr` 设置回位图的开头。

每个像素都是内存的 4 个字节，因此必须单独设置每个字节。 此处的代码假定字节的顺序为红色、绿色、蓝色和 alpha，这与 `SKColorType.Rgba8888` 的颜色类型一致。 你可能还记得这是适用于 iOS 和 Android，而不是通用 Windows 平台的默认颜色类型。 默认情况下，UWP 使用 `SKColorType.Bgra8888` 颜色类型创建位图。 出于此原因，会在该平台上看到一些不同的结果 ！

可以将从 `ToPointer` 返回的值强制转换为 `uint` 指针，而不是 `byte` 指针。 这允许要在一个语句中访问整个像素。 将 `++` 运算符应用到该指针会按四个字节递增，以指向下一个像素：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

该像素是使用 `MakePixel` 方法设置的，该方法用红色、绿色、蓝色和 alpha 分量构造一个整数像素。 请记住，`SKColorType.Rgba8888` 格式具有如下所示的像素字节顺序：

RR GG BB AA

但这些字节相对应的整数：

AABBGGRR

根据小字节序体系结构首先存储最低有效字节的整数。 对于具有 `Bgra8888` 颜色类型的位图，此 `MakePixel` 方法将不能正常工作。

使用[`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions)选项标记 `MakePixel` 方法，以鼓励编译器避免使此方法为单独的方法，而是编译调用方法的代码。 这应提高性能。

有趣的是，`SKColor` 结构定义了从 `SKColor` 到无符号整数的显式转换，这意味着可创建 `SKColor` 值，并且可以使用 `uint` 转换，而不是 `MakePixel`：

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

唯一的问题是：是按 `SKColorType.Rgba8888` 颜色类型或 `SKColorType.Bgra8888` 颜色类型的顺序 `SKColor` 值的整数格式，还是完全是其他内容？ 应很快显示该问题的答案。

### <a name="the-setpixels-method"></a>SetPixels 方法

`SKBitmap` 还定义了一个名为[`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr))的方法，如下所示：

```csharp
bitmap.SetPixels(intPtr);
```

请记住，`GetPixels` 获取了引用位图用来存储其像素的内存块的 `IntPtr`。 `SetPixels` 调用将该内存块_替换_为指定为 `SetPixels` 参数的 `IntPtr` 所引用的内存块。 位图将释放以前使用的内存块。 下次调用 `GetPixels` 时，它将获取用 `SetPixels`设置的内存块。

最初，与 `GetPixels` 相比，`SetPixels` 比提供更多的功能和性能。 使用 `GetPixels` 获取位图内存块并对其进行访问。 在 `SetPixels` 分配并访问一些内存，然后将其设置为位图内存块。

但使用 `SetPixels` 提供了独特的语法优势：它允许使用数组访问位图像素位。 下面是 `GradientBitmapPage` 中演示此方法的方法。 该方法首先定义对应的位图的像素的字节的多维度的字节数组。 第一个维度是行，第二个维度列和第三个维度对应于每个像素的四个组件：

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

然后，在使用像素填充数组后，将使用 `unsafe` 块和 `fixed` 语句来获取指向此数组的字节指针。 然后，可以将该字节指针强制转换为要传递到 `SetPixels`的 `IntPtr`。

您创建的数组不需要的字节数组。 它可以是具有行和列只有两个维度的整数数组：

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

再次使用 `MakePixel` 方法将颜色组件合并为32位像素。

只是为了完整起见，下面是相同的代码，但将 `SKColor` 值强制转换为无符号整数：

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>比较方法

"**渐变颜色**" 页的构造函数将调用上面所示的所有8个方法，并保存结果：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

构造函数通过创建 `SKCanvasView` 来显示生成的位图来结束。 `PaintSurface` 处理程序将其图面划分为八个矩形，并调用 `Display` 来显示每个矩形：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

为了允许编译器优化代码，此页在**发布**模式下运行。 下面是在 MacBook Pro、 Nexus 5 Android 手机和运行 Windows 10 Surface Pro 3 iPhone 8 模拟器上运行该页面。 由于存在硬件差异，避免比较设备之间的性能时间，但改为查看相对时间，每个设备上：

[![渐变位图](pixel-bits-images/GradientBitmap.png "渐变位图")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

下面是将合并执行时间以毫秒为单位的表：

| API       | 数据类型 | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3.49 |
| 像素    |           | 0.32 |    1.23 | 0.07 |
| GetPixels | 字节      | 0.09 |    价格为 $0.24 | 0.10 |
|           | uint      | 0.06 |    0.26 之间 | 0.05 |
|           | SKColor   | 0.29 |    0.99 | 0.07 |
| SetPixels | 字节      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0.14 |    0.69 | 0.06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

如预期那样，调用 `SetPixel` 65536 次是设置位图像素的最 effeicient 方法。 填充 `SKColor` 数组和设置 `Pixels` 属性更好，甚至比较了某些 `GetPixels` 和 `SetPixels` 技术。 通常，使用 `uint` 像素值的速度要快于设置单独 `byte` 组件的速度，并将 `SKColor` 值转换为无符号整数会增加进程的一些开销。

它也是值得关注要比较各种渐变： 每个平台的前行是相同的并按预期显示渐变。 这意味着 `SetPixel` 方法和 `Pixels` 属性将从颜色中正确创建像素，而不考虑基础像素格式。

IOS 和 Android 屏幕截图的后两行也是相同的，它确认为这些平台的默认 `Rgba8888` 像素格式正确定义了小 `MakePixel` 方法。

IOS 和 Android 屏幕截图的底部行向后，这表示通过强制转换 `SKColor` 值获得的无符号整数的形式为：

AARRGGBB

字节的顺序：

BB GG RR AA

这是 `Bgra8888` 排序，而不是 `Rgba8888` 排序。 `Brga8888` 格式是通用 Windows 平台的默认格式，这就是此屏幕截图最后一行的渐变与第一行相同的原因。 但中间两行不正确，因为创建这些位图的代码假定 `Rgba8888` 排序。

如果要使用相同的代码来访问每个平台上的像素位，则可以使用 `Rgba8888` 或 `Bgra8888` 格式显式创建 `SKBitmap`。 如果要将 `SKColor` 值强制转换为位图像素，请使用 `Bgra8888`。

## <a name="random-access-of-pixels"></a>随机访问的像素为单位

**渐变位图**页中的 `FillBitmapBytePtr` 和 `FillBitmapUintPtr` 方法从设计为按顺序填充位图的 `for` 循环（从上到下行，在每行中从左到右）中受益。 可以使用递增指针在同一语句设置像素。

有时是需要随机而不是按顺序访问像素。 如果使用的是 `GetPixels` 方法，则需要根据行和列计算指针。 这在**彩虹正弦**页面中进行了演示，这会创建一个位图，其中显示一条正弦曲线循环的外形。

出喷薄彩虹的颜色是最简单的方法创建使用 HSL （色调、 饱和度、 亮度） 颜色模型。 `SKColor.FromHsl` 方法使用介于0到360之间的色调值（例如圆圈的角度，而从红色、绿色和蓝色，返回红色）以及从0到100的饱和度和发光度值来创建 `SKColor` 值。 对于出喷薄彩虹的颜色，饱和度应设置为最多为 100、 50 的中点的亮度。

**彩虹正弦**通过循环遍历位图的各个行，然后循环到360的色调值，来创建此图像。 从每个的色调值，它会计算也基于正弦值的位图列：

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
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

请注意，构造函数基于 `SKColorType.Bgra8888` 格式创建位图：

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

这使得程序无需担心即可使用 `SKColor` 值到 `uint` 像素的转换。 尽管它在此特定程序中不起作用，但当你使用 `SKColor` 转换设置像素时，还应指定 `SKAlphaType.Unpremul`，因为 `SKColor` 不会按 alpha 值预乘其颜色成分。

然后，构造函数使用 `GetPixels` 方法获取指向位图第一个像素的指针：

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

对于任何特定的行和列，必须将偏移值添加到 `basePtr`。 此偏移量是时间位图宽度，以及列的行：

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

使用此指针将 `SKColor` 值存储在内存中：

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

在 `SKCanvasView`的 `PaintSurface` 处理程序中，该位图会拉伸以填充显示区域：

[![彩虹正弦](pixel-bits-images/RainbowSine.png "彩虹正弦")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>从到另一个位图

很多图像处理任务都涉及修改才会传输到另一个从一个位图的像素为单位。 此方法在 "**颜色调整**" 页中进行演示。 页面加载某个位图资源，然后允许使用三个 `Slider` 视图修改图像：

[![颜色调整](pixel-bits-images/ColorAdjustment.png "颜色调整")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

对于每个像素颜色，第一个 `Slider` 向色相添加介于0到360之间的值，但随后使用取模运算符将结果保持为0到360之间，从而有效地沿着光谱上的颜色偏移（如 UWP 屏幕截图所示）。 第二个 `Slider` 允许您选择要应用于饱和度的0.5 到2之间的乘法系数，第三个 `Slider` 对发光度执行相同的工作，如 Android 屏幕截图所示。

程序将保留两个位图，即名为 `srcBitmap` 的原始源位图和名为 `dstBitmap`的调整后目标位图。 每次移动 `Slider` 时，程序都会在 `dstBitmap`中计算所有新像素。 当然，用户将通过非常快速地移动 `Slider` 视图进行试验，因此你希望获得可管理的最佳性能。 这涉及源和目标位图的 `GetPixels` 方法。

**颜色调整**页面不会控制源和目标位图的颜色格式。 相反，它包含 `SKColorType.Rgba8888` 和 `SKColorType.Bgra8888` 格式略有不同的逻辑。 源和目标可以是不同的格式，并且程序仍将起作用。

下面是程序的关键 `TransferPixels` 方法除外，这种方法会将像素转换为目标。 构造函数将 `dstBitmap` 等于 `srcBitmap`。 `PaintSurface` 处理程序显示 `dstBitmap`：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

`Slider` 视图的 `ValueChanged` 处理程序将计算调整值并调用 `TransferPixels`。

整个 `TransferPixels` 方法标记为 `unsafe`。 它通过获取这两个位图的像素位的字节指针开始，然后循环访问所有行和列。 从源位图，此方法获取每个像素的四个字节。 它们可以是 `Rgba8888` 或 `Bgra8888` 顺序。 检查颜色类型是否允许创建 `SKColor` 值。 然后提取并调整 HSL 组件，并将其用于重新创建 `SKColor` 值。 根据目标位图是 `Rgba8888` 还是 `Bgra8888`，这些字节将存储在目标 bitmp 中：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

很可能此方法的性能可以通过创建单独的源和目标位图的颜色类型的各种组合的方法更多改进，并且避免检查每个像素的类型。 另一种方法是根据颜色类型为 `col` 变量提供多个 `for` 循环。

## <a name="posterization"></a>海报

其他涉及访问像素位的常见任务是_posterization_。 如果颜色编码以位图的像素为单位数量减少，使结果类似于使用有限的调色板的手绘海报。

"**色调分离**" 页对其中一个猴子映像执行此过程：

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

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
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

构造函数中的代码访问每个像素，执行按位与运算，值 0xE0E0E0FF，然后将结果存储在位图中返回。 值 0xE0E0E0FF 保留高 3 位的每个颜色组件，并将较低的 5 位设置为 0。 位图的颜色缩减<sup>为2或</sup>512，而不是 2<sup>24</sup>或16777216色：

[![色调](pixel-bits-images/Posterize.png "色调")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
