---
title: Xamarin.Forms 双屏
description: 本指南介绍了如何构建适用于双屏设备的 Xamarin.Forms 应用。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 344b6293090ffa4281ea6351f7f176a5be37e5bd
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165557"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms 双屏

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) 和 Surface Neo (Windows 10X) 引入了适合应用触控的新模式。 Xamarin.Forms 包含 `TwoPaneView` 和 `DualScreenInfo` 类，因此你可以为这些设备开发应用程序。

## <a name="dual-screen-design-patterns"></a>[双屏设计模式](design-patterns.md)

在考虑如何以最佳方式在双屏设备上使用多个屏幕时，请参考该模式指南，查找最适合你的应用程序界面的设置。

## <a name="dual-screen-layout"></a>[双屏布局](twopaneview.md)

Xamarin.Forms `TwoPaneView` 类是一种针对双屏设备进行优化的跨平台布局，设计灵感源自 UWP 控件。

## <a name="dual-screen-device-capabilities"></a>[双屏设备功能](dual-screen-info.md)

`DualScreenInfo` 类可用于确定你的视图位于哪个窗格中、视图的大小如何、设备的放置方向如何，以及铰链的角度等等。

## <a name="dual-screen-platform-helpers"></a>[双屏平台帮助程序](dual-screen-helper.md)

通过 `DualScreenHelper` 类，可查看平台是否支持以画中画的形式打开新窗口。 在 Neo 中，你可用它来打开一个当设备处于撰写模式下时在 Wonderbar 中显示的窗口。

## <a name="dual-screen-triggers"></a>[双屏触发器](triggers.md)

[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) 命名空间包含两个状态触发器，这两个触发器在附加布局的视图模式或窗口发生改变时会触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。
