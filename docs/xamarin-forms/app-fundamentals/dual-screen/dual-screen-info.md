---
title: Xamarin.Forms DualScreenInfo
description: 本指南介绍了如何使用 Xamarin.Forms DualScreenInfo 来优化 Surface Duo 和 Surface Neo 等双屏设备的应用体验。
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: cae704b7d6300a9dc5eb456f0dec8989813c6581
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145611"
---
# <a name="xamarinforms-dualscreeninfo"></a>Xamarin.Forms DualScreenInfo

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

DualScreenInfo 可用于检测和通知当前与双屏相关的更改和布局。

## <a name="properties"></a>属性

- `SpanningBounds`：横跨两个屏幕时，这将返回两个矩形，表示每个可见区域的边界。 如果窗口未跨屏显示，这将返回一个空数组。
- `HingeBounds`：铰链在屏幕上的位置。
- `IsLandscape`：指示设备是否横向显示。 这很有用，因为应用程序跨屏显示时，本机方向 API 不会正确报告方向。
- `PropertyChanged`：当任何属性更改时触发。
- `SpanMode`：指示布局处于 Tall、Wide 还是 SinglePane 模式下。

## <a name="android-only-property"></a>仅限 Android 的属性

仅在从 Android 平台项目中访问 DualScreenInfo 时，才可使用此属性。
你可通过自定义呈现器使用此属性。

- `GetHingeAngleAsync` 会检索设备铰链的当前角度。 使用模拟器时，可通过修改压力传感器来设置 HingeAngle。

## <a name="android-custom-renderer-for-polling-hinge-angle"></a>用于轮询铰链角度的 Android 自定义呈现器

```csharp
public class HingeAngleLabelRenderer : Xamarin.Forms.Platform.Android.FastRenderers.LabelRenderer
{
    System.Timers.Timer _hingeTimer;
    public HingeAngleLabelRenderer(Context context) : base(context)
    {
    }

    async void OnTimerElapsed(object sender, System.Timers.ElapsedEventArgs e)
    {
        if (_hingeTimer == null)
            return;

        _hingeTimer.Stop();
        var hingeAngle = await DualScreenInfo.Current.GetHingeAngleAsync();

        Device.BeginInvokeOnMainThread(() =>
        {
            if (_hingeTimer != null)
                Element.Text = hingeAngle.ToString();
        });

        if (_hingeTimer != null)
            _hingeTimer.Start();
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
    {
        base.OnElementChanged(e);

        if (_hingeTimer == null)
        {
            _hingeTimer = new System.Timers.Timer(100);
            _hingeTimer.Elapsed += OnTimerElapsed;
            _hingeTimer.Start();
        }
    }

    protected override void Dispose(bool disposing)
    {
        if (_hingeTimer != null)
        {
            _hingeTimer.Elapsed -= OnTimerElapsed;
            _hingeTimer.Stop();
            _hingeTimer = null;
        }

        base.Dispose(disposing);
    }
}
```

## <a name="access-dualscreeninfo-for-application-window"></a>访问应用程序窗口的 DualScreenInfo

```csharp
DualScreenInfo currentWindow = DualScreenInfo.Current;

// Retrieve absolute position of the hinge on the screen
var hingeBounds = currentWindow.HingeBounds;

// check if app window is spanned across two screens
if(currentWindow.SpanMode == TwoPaneViewMode.SinglePane)
{
    // window is only on one screen
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Tall)
{
    // window is spanned across two screens and oriented as landscape
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented as portrait
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-your-own-layouts"></a>将 DualScreenInfo 应用到自己的布局

DualScreenInfo 有一个构造函数，它可采用布局并向你显示与设备双屏相关的布局的信息。

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}"></ColumnDefinition>
        <ColumnDefinition Width="{Binding Column2Width}"></ColumnDefinition>
        <ColumnDefinition Width="{Binding Column3Width}"></ColumnDefinition>
    </Grid.ColumnDefinitions>

    <Label FontSize="Large" VerticalOptions="Center" HorizontalOptions="End" Text="I should be on the left side of the hinge"></Label>
    <Label FontSize="Large" VerticalOptions="Center" HorizontalOptions="Start" Grid.Column="2" Text="I should be on the right side of the hinge"></Label>
</Grid>
```

```csharp
public partial class GridUsingDualScreenInfo : ContentPage
{
    public DualScreenInfo DualScreenInfo { get; }
    public double Column1Width { get; set; }
    public double Column2Width { get; set; }
    public double Column3Width { get; set; }

    public GridUsingDualScreenInfo()
    {
        InitializeComponent();
        DualScreenInfo = new DualScreenInfo(grid);
        BindingContext = this;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        DualScreenInfo.PropertyChanged += OnInfoPropertyChanged;
        UpdateColumns();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        DualScreenInfo.PropertyChanged -= OnInfoPropertyChanged;
    }

    void UpdateColumns()
    {
        // Check if grid is on two screens
        if (DualScreenInfo.SpanningBounds.Length > 0)
        {
            // set the width of the first column to the width of the layout
            // that's on the left screen
            Column1Width = DualScreenInfo.SpanningBounds[0].Width;

            // set the middle column to the width of the hinge
            Column2Width = DualScreenInfo.HingeBounds.Width;

            // set the width of the third column to the width of the layout
            // that's on the right screen
            Column3Width = DualScreenInfo.SpanningBounds[1].Width;
        }
        else
        {
            Column1Width = 100;
            Column2Width = 0;
            Column3Width = 100;
        }

        OnPropertyChanged(nameof(Column1Width));
        OnPropertyChanged(nameof(Column2Width));
        OnPropertyChanged(nameof(Column3Width));

    }

    void OnInfoPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
    {
        UpdateColumns();
    }
}
```

![](dual-screen-info-images/grid-on-two-screens.png "Positioning Grid on Two Screens")

## <a name="related-links"></a>相关链接

- [（示例）](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
