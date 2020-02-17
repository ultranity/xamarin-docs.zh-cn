---
title: Xamarin.Forms 双屏功能
description: 本指南介绍了 Xamarin.Forms 如何可轻松启发适合双屏设备的应用。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1f648297a608c592d90f2c70494ae4496fe73c0f
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145511"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms 双屏

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) 和 Surface Neo (Windows 10X) 引入了适合应用触控的新模式。 Xamarin.Forms 引入了 `TwoPaneView` 和 `DualScreenInfo` 来帮助你充分利用这些新体验。

## <a name="dual-screen-patternsdesign-patternsmd"></a>[双屏模式](design-patterns.md)

在考虑如何以最佳方式在双屏设备上使用多个屏幕时，请参考我们的模式指南，查找最适合你的应用程序界面的设置。

## <a name="twopaneviewtwopaneviewmd"></a>[TwoPaneView](twopaneview.md)

Xamarin.Forms `TwoPaneView` 类是以同名的 UWP 控件为灵感而设计的，它引入了一种在跨平台方式中适合双屏设备的布局。

## <a name="dualscreeninfodual-screen-infomd"></a>[DualScreenInfo](dual-screen-info.md)

要充分利用双屏设备，你可能需要了解你的视图在哪个窗格中、视图的大小如何、设备的放置方向如何，以及铰链的角度等等。 `DualScreenInfo` 提供了此信息。

## <a name="dualscreenhelperdual-screen-helpermd"></a>[DualScreenHelper](dual-screen-helper.md)
通过 `DualScreenHelper`可查看平台是否支持以画中画的形式打开新窗口。 在 Neo 中，你可用它来打开一个当设备处于撰写模式下时在 Wonderbar 中显示的窗口。