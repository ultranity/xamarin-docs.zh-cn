---
title: Xamarin.Essentials 平台扩展
description: 必须使用矩形、大小和点等平台类型时，Xamarin.Essentials 提供了几种平台扩展方法。
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545147"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials:平台扩展

必须使用矩形、大小和点等平台类型时，Xamarin.Essentials 提供了几种平台扩展方法。 这意味着可以针对其 iOS、Android 和 UWP 特定类型在这些类型的 `System` 版本之间进行转换。 

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>使用平台扩展

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

所有平台扩展只能从 iOS、Android 或 UWP 项目中调用。

## <a name="android-extensions"></a>Android 扩展

只能从 Android 项目访问这些扩展。

### <a name="application-context--activity"></a>应用 Context 和 Activity

使用 `Platform` 类中的平台扩展，可以访问正在运行的应用的当前 `Context` 或 `Activity`。

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

如果需要 `Activity`，但应用尚未完全启动，应使用 `WaitForActivityAsync` 方法。

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>活动生命周期

除了获取当前 Activity 之外，还可以注册生命周期事件。

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

Activity 状态如下所示：

* 创建时间
* Resumed
* 已暂停
* 已破坏
* SaveInstanceState
* 已开始
* 已停止

有关详细信息，请参阅 [Activity 生命周期](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/)文档。

## <a name="ios-extensions"></a>iOS 扩展

只能从 iOS 项目访问这些扩展。

### <a name="current-uiviewcontroller"></a>当前 UIViewController

获取对当前可见 `UIViewController` 的访问权限：

```csharp
var vc = Platform.GetCurrentUIViewController();
```

如果无法检测 `UIViewController`，此方法返回 `null`。

## <a name="cross-platform-extensions"></a>跨平台扩展

这些扩展存在于每个平台中。

### <a name="point"></a>点

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>大小

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>矩形

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [转换器源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [点转换器 API 文档](xref:Xamarin.Essentials.PointExtensions)
- [矩形转换器 API 文档](xref:Xamarin.Essentials.RectangleExtensions)
- [大小转换器 API 文档](xref:Xamarin.Essentials.SizeExtensions)
