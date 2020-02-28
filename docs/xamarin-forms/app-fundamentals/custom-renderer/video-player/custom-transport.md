---
title: 自定义视频传输控件
description: 本文介绍如何使用 Xamarin.Forms 在视频播放器应用程序中实现自定义传输控件。
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: efe41fa5f25f6257587fd97a2711e9037b94dc6e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636011"
---
# <a name="custom-video-transport-controls"></a>自定义视频传输控件

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

视频播放器的传输控件包括执行“播放”、“暂停”和“停止”功能的按钮    。 这些按钮通常使用熟悉的图标而非文本来标识，且“播放”和“暂停”功能通常合并成一个按钮   。

默认情况下，`VideoPlayer` 显示每个平台支持的传输控件。 当你将 `AreTransportControlsEnabled` 属性设置为 `false` 时，将禁止这些控件。 然后，可以通过编程控制 `VideoPlayer` 或提供自己的传输控件。

## <a name="the-play-pause-and-stop-methods"></a>Play、Pause 和 Stop 方法

`VideoPlayer` 类定义了三个名为 `Play`、`Pause` 和 `Stop` 的方法，它们是通过触发以下事件实现的：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

这些事件的事件处理程序由每个平台中的 `VideoPlayerRenderer` 类设置，如下所示：

### <a name="ios-transport-implementations"></a>iOS 传输实现

iOS 版本的 `VideoPlayerRenderer` 在 `NewElement` 属性不是 `null` 时使用 `OnElementChanged` 方法为这三个事件设置处理程序，在 `OldElement` 不是 `null` 时拆离事件处理程序：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

事件处理程序是通过对 `AVPlayer` 对象调用方法实现的。 由于没有用于 `AVPlayer` 的 `Stop` 方法，所以它是通过暂停视频并将位置移动到开始处来模拟的。

### <a name="android-transport-implementations"></a>Android 传输实现

Android 实现与 iOS 实现类似。 这三个功能的处理程序在 `NewElement` 不是 `null` 时设置，在 `OldElement` 不是 `null` 时拆离：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

这三个功能调用由 `VideoView` 定义的方法。

### <a name="uwp-transport-implementations"></a>UWP 传输实现

这三个传输功能的 UWP 实现与 iOS 和 Android 实现非常相似：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>视频播放器状态

实现“播放”、“暂停”、“停止”功能不足以支持传输控件    。 通常“播放”和“暂停”命令都是通过同一个按钮实现的，该按钮会更改外观，以指示视频当前是播放还是暂停   。 此外，如果视频尚未加载，则不应启用该按钮。

这些要求意味着视频播放器需要提供一个当前状态，以指示它是否正在播放或暂停，或者是否还没有准备好播放视频。 （每个平台还支持一些属性，这些属性指示视频是否可以暂停，或者是否可以移动到新的位置，但是这些属性适用于流式处理视频，而不是视频文件，因此这里描述的 `VideoPlayer` 中不支持这些属性。）

“VideoPlayerDemos”项目包括具有三个成员的 `VideoStatus` 枚举  ：

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

`VideoPlayer` 类定义了一个类型为 `VideoStatus` 的可绑定属性 `Status`。 此属性定义为只读，因为它只能从平台呈现器中设置：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

通常，只读可绑定属性在 `Status` 属性上具有私有的 `set` 访问器，以允许在类中设置它。 但是，对于由呈现器支持的 `View` 派生，属性必须从类外部设置，但只能由平台呈现器设置。

出于此原因，另一个属性被定义为 `IVideoPlayerController.Status`。 这是一个显式接口实现，由 `VideoPlayer` 类实现的 `IVideoPlayerController` 接口使之成为可能：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

这类似于 [`WebView`](xref:Xamarin.Forms.WebView) 控件如何使用 [`IWebViewController`](xref:Xamarin.Forms.IWebViewController) 接口实现 `CanGoBack` 和 `CanGoForward` 属性。 （详见 [`WebView`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) 及其呈现器的源代码。）

这使得 `VideoPlayer` 外部的类可以通过引用 `IVideoPlayerController` 接口来设置 `Status` 属性。 （稍后你将看到代码。）该属性也可以从其他类中设置，但不太可能会在无意中设置。 最重要的是，不能通过数据绑定设置 `Status` 属性。

若要帮助呈现器更新 `Status` 属性，`VideoPlayer` 类定义了一个 `UpdateStatus` 事件，该事件每隔 0.1 秒触发一次：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>iOS 状态设置

iOS `VideoPlayerRenderer` 为 `UpdateStatus` 事件设置了一个处理程序（在没有基础 `VideoPlayer` 元素时拆离该处理程序），并使用该处理程序设置 `Status` 属性：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

必须访问 `AVPlayer` 的两个属性：类型 `AVPlayerStatus` 的 [`Status`](xref:AVFoundation.AVPlayer.Status*) 属性和类型 `AVPlayerTimeControlStatus` 的 [`TimeControlStatus`](xref:AVFoundation.AVPlayer.TimeControlStatus*) 属性。 注意，`Element` 属性（即 `VideoPlayer`）必须转换为 `IVideoPlayerController` 才能设置 `Status` 属性。

### <a name="the-android-status-setting"></a>Android 状态设置

Android `VideoView` 的 [`IsPlaying`](xref:Android.Widget.VideoView.IsPlaying) 属性是一个布尔值，仅指示视频是播放或暂停。 若要确定 `VideoView` 是否不能播放或暂停视频，必须处理 `VideoView` 的 `Prepared` 事件。 这两个处理程序在 `OnElementChanged` 方法中设置，并在 `Dispose` 重写期间拆离：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`UpdateStatus` 处理程序使用 `isPrepared` 字段（在 `Prepared` 处理程序中设置）和 `IsPlaying` 属性来设置 `Status` 属性：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>UWP 状态设置

UWP `VideoPlayerRenderer` 使用 `UpdateStatus` 事件，但不需要它即可设置 `Status` 属性。 `MediaElement` 定义了一个 [`CurrentStateChanged`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentStateChanged) 事件，该事件允许在 [`CurrentState`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentState*) 属性更改时通知呈现器。 属性在 `Dispose` 重写中拆离：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`CurrentState` 属性类型为 [`MediaElementState`](/uwp/api/windows.ui.xaml.media.mediaelementstate)，并且很容易映射到 `VideoStatus`：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>播放、暂停和停止按钮

使用表示“播放”、“暂停”和“停止”图像的 Unicode 字符时会出现问题    。 Unicode 标准的[杂类技术](https://unicode-table.com/en/blocks/miscellaneous-technical/)部分定义了三个似乎适用于此用途的符号字符。 这些是：

- 0x23F5（黑色中等直角三角形）或 &#x23F5；表示“播放” 
- 0x23F8（双竖线）或 &#x23F8；表示“暂停” 
- 0x23F9 （黑色方框）或 &#x23F9；表示“停止” 

不管这些符号在浏览器中是如何显示的（不同的浏览器处理方式不同），它们在 Xamarin.Forms 支持的平台上的显示并不一致。 在 iOS 和 UWP 设备上，“暂停”和“停止”字符具有蓝色 3D 背景和白色前景的图形外观   。 但在 Android 上却不是这样，它的符号只是蓝色。 但是，“播放”的 0x23F5 码点在 UWP 上没有相同的外观，iOS 和 Android 甚至不支持它  。

因此，0x23F5 码点不能用于“播放”  。 一个不错的替代品是：

- 0x25B6（黑色直角三角形）或 &#x25B6；表示“播放” 

每个平台都支持此功能，除了它是一个简单的黑色三角形，不像“暂停”和“停止”的 3D 外观   。 一种可能性是在 0x25B6 码点之后添加一个变体代码：

- 0x25B6 后面跟着 0xFE0F（变量 16）或 &#x25B6；&#xFE0F；表示“播放” 

这就是如下所示标记中使用的内容。 在 iOS 系统中，“播放”符号与“暂停”和“停止”按钮具有相同的 3D 外观，但在 Android 和 UWP 上无法使用    。

“自定义传输”页面将“AreTransportControlsEnabled”属性设置为“false”，并包括加载视频时显示的 `ActivityIndicator` 和两个按钮    。 `DataTrigger` 对象用于启用和禁用 `ActivityIndicator` 和按钮，并在“播放”和“暂停”之间切换第一个按钮   ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

[数据触发器](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers)一文中详细介绍了数据触发器。

代码隐藏文件包含按钮 `Clicked` 事件的处理程序：

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

由于“CustomTransport.xaml”文件中的 `AutoPlay` 设置为 `false`，因此启用视频后，需要按下“播放”按钮，   。 定义按钮使得上述 Unicode 字符附带其文本等效项。 播放视频时，按钮在每个平台上都具有一致的外观：

[![自定义传输正在播放](custom-transport-images/customtransportplaying-small.png "自定义传输正在播放")](custom-transport-images/customtransportplaying-large.png#lightbox "自定义传输正在播放")

但在 Android 和 UWP 上，当视频暂停时，“播放”按钮看起来大不相同  ：

[![自定义传输已暂停](custom-transport-images/customtransportpaused-small.png "自定义传输已暂停")](custom-transport-images/customtransportpaused-large.png#lightbox "自定义传输已暂停")

在生产应用程序中，你可能希望使用自己的位图作为按钮来实现视觉对象一致性。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
