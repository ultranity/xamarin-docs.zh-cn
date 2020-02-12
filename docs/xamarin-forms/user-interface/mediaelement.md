---
title: Xamarin. Forms MediaElement
description: 本文介绍如何使用 MediaElement 在 Xamarin. Forms 应用程序中播放视频和音频。
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/7/2020
ms.openlocfilehash: 67e4e9eec38f9dd23ebc3efe503d4b2a34ea8b39
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145717"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin. Forms MediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/WorkingWithMediaElement)

`MediaElement` 呈现播放视频和音频的媒体播放机。 可以使用操作系统播放控件（称为传输控件），也可以隐藏这些控件，并提供您自己的控件来满足您的设计要求。

使用此新的预览控件之前，必须通过在 `App.xaml.xs`中设置相应的标志来选择使用它：

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

支持的平台：

- Android
- iOS
- UWP
- WPF
- macOS
- Tizen

## <a name="set-media-source"></a>设置媒体源

`MediaElement` 属性 `Source` 可以采用 URI 或本地文件路径。 播放会在媒体打开时立即开始播放。

```xaml
<MediaElement Source="http://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4" />
```

![](mediaelement-images/mediaelement-android.png "MediaElement on Android")

若要从平台项目将源设置为本地资产，请使用 "ms-appx" URI 方案。

```xaml
<MediaElement Source="ms-appx://XamarinShow_mid.mp4" />
```

使用数据绑定时，可能要使用值转换器来应用此 URI 方案：

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (String.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        throw new NotImplementedException();
    }
}
```

```xaml
<MediaElement
    Source="{Binding VideoSource, Converter={StaticResource VideoSourceConverter}}" />
```

## <a name="control-playback"></a>控制播放

默认情况下，`MediaElement` 将使用平台本机播放控制播放/暂停、音量和静音、查找和时间显示。

若要重写和实现自己的控件，请先通过设置 `ShowsPlaybackControls="False"`禁用平台控件。 现在，你可以使用自己的控件来反映播放状态，并使用以下属性、方法和事件来控制播放：

- `Play()`、`Pause()``Stop()` 方法来控制播放
- `AutoPlay` 设置初始行为
- `CanSeek` 启用或禁用查找
- `Position` 和 `Duration` 时间属性
- 更改音量和静音的 `Volume` 属性
- 播放完成时的重复 `IsLooping` 属性
- 用于处理 UI 更新的 `StateRequested`、`PositionRequested``VolumeRequested`

### <a name="handle-additional-media-events"></a>处理其他媒体事件

你可以响应常见媒体事件，例如 `MediaOpened`、`MediaEnded`、`MediaFailed`和 `CurrentStateChanged` 事件。 最好始终处理 MediaFailed 事件。
CurrentStateChanged 给出有关播放状态的信息。 使用 `MediaElementState` 枚举的值：

- **已关闭**：不包含媒体并显示透明帧
- 正在**打开**：正在验证并尝试加载指定的源
- **缓冲**：正在加载要播放的媒体
  - 在此状态下，其 `Position` 不会前进
  - 如果正在播放视频，它将继续显示最后显示的帧
- 正在**播放**：正在播放当前媒体源
- 已**暂停**：不提升其 `Position`
  - 如果播放视频，它将继续显示当前帧
- **已停止**：包含媒体，但未播放或暂停
  - 其 `Position` 为0，并且不能前进
  - 如果加载的媒体是视频，则 `MediaElement` 将显示第一帧。

## <a name="control-display"></a>控件显示

与[`Image`](xref:Xamarin.Forms.Image)类似，`MediaElement` 控件具有 `VideoHeight`、`VideoWidth`和 `Aspect`。 方面采用三个不同的选项：

- **AspectFill**：该视频将填充控件的整个宽度和高度，通常在控件边界外出血，同时保持源方面
- **AspectFit**：视频可在控件的宽度和高度内容纳，同时保持源方面
- **填充**：视频将填充控件的宽度和高度

## <a name="additional-properties"></a>其他属性

- `VideoWidth`：控件的宽度
- `VideoHeight`：控件的高度
- `KeepScreenOn`：如果设备屏幕在播放过程中应保持打开

## <a name="related-links"></a>相关链接

- [MediaElement （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/WorkingWithMediaElement)
