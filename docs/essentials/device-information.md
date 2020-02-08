---
title: Xamarin.Essentials:设备信息
description: 本文档介绍 Xamarin.Essentials 中的 DeviceInfo 类，此类提供有关运行应用程序的设备的信息。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 29b67e4410a3549e4e2cd63121ffa2e2205e0941
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940806"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials:设备信息

DeviceInfo 类提供有关运行应用程序的设备的信息。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过 API 公开了以下信息：

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platforms"></a>平台

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform) 与映射到操作系统的一个常量字符串相关联。 可以使用 `DevicePlatform` 结构检查以下值：

- DevicePlatform.iOS – iOS
- DevicePlatform.Android – Android
- DevicePlatform.UWP – UWP
- DevicePlatform.Unknown – 未知

## <a name="idioms"></a>习惯用语

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom) 与映射到运行应用程序的设备类型的一个常量字符串相关联。 可以使用 `DeviceIdiom` 结构检查以下值：

- DeviceIdiom.Phone – 电话
- DeviceIdiom.Tablet – 平板电脑
- DeviceIdiom.Desktop – 台式计算机
- DeviceIdiom.TV – TV
- DeviceIdiom.Watch – 手表
- DeviceIdiom.Unknown – 未知

## <a name="device-type"></a>设备类型

`DeviceInfo.DeviceType` 关联一个枚举以确定应用程序是在物理设备还是虚拟设备上运行。 虚拟设备是指模拟器或仿真程序。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS 不会向开发人员公开一个 API 来获取特定 iOS 设备的名称。 而是会返回一个硬件标识符，例如 iPhone10,6，这是指 iPhone X。这些标识符的映射不是由 Apple 提供，但可以在 [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models)（一个非官方来源）上找到。

--------------

## <a name="api"></a>API

- [DeviceInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文档](xref:Xamarin.Essentials.DeviceInfo)
