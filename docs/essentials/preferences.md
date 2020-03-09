---
title: Xamarin.Essentials:首选项
description: 本文档介绍 Xamarin.Essentials 中的 Preferences 类，此类将应用程序首选项保存在键/值存储中。 本文还讨论了如何使用类和可以存储的数据类型。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 01/15/2019
ms.custom: video
ms.openlocfilehash: c7d4e4f7ea27e150752b222e3ea4ce2c4256a43a
ms.sourcegitcommit: 099b06e311a40c00eeea85465ff9b97867a5c5de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295399"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials:首选项

Preferences 类帮助将应用程序首选项存储在键/值存储中  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>使用 Preferences

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

将给定密匙的值保存在首选项中  ：

```csharp
Preferences.Set("my_key", "my_value");
```

从首选项检索值或检索默认值（如果未设置）：

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

检查首选项中是否存在特定密钥  ：

```csharp
bool hasKey = Preferences.HasKey("my_key");
```

从首选项删除密钥  ：

```csharp
Preferences.Remove("my_key");
```

删除所有首选项：

```csharp
Preferences.Clear();
```

除了这些方法外，每个方法都采用一个可选的 `sharedName`，可用于创建首选的其他容器。 查看下面的平台实现细节。

## <a name="supported-data-types"></a>支持的数据类型

以下数据类型在 Preferences 中受到支持  ：

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>实现详细信息

`DateTime` 的值采用 `DateTime` 类定义的两种方法按 64 位二进制（长整型）格式进行存储：[`ToBinary`](xref:System.DateTime.ToBinary) 方法用于编码 `DateTime` 值，[`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) 方法对值进行解码。 当存储的 `DateTime` 不是协调世界时 (UTC) 值时，请参阅这些方法的相关文档以了解如何进行对值解码的调整。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="android"></a>[Android](#tab/android)

所有数据都存储到[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)中。 如果未指定 `sharedName`，则使用默认的共享首选项，否则此名称将用于获取具有指定名称的私有共享首选项  。

# <a name="ios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/xamarin/ios/app-fundamentals/user-defaults) 用于将值存储在 iOS 设备上。 如果未指定 `sharedName`，则使用 `StandardUserDefaults`，否则此名称将用于创建具有用于 `NSUserDefaultsType.SuiteName` 的指定名称的新 `NSUserDefaults`。

# <a name="uwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) 用于将值存储在设备上。 如果未指定 `sharedName`，则使用 `LocalSettings`，否则此名称将用于在 `LocalSettings` 内创建新容器。 

`LocalSettings` 还存在以下限制：每个设置的名称长度最多可为 255 个字符。 每个设置的大小最多可为 8K 字节，每个复合设置的大小最多可为 64K 字节。

--------------

## <a name="persistence"></a>持久性

卸载应用程序将导致所有首选项被删除  。 对此有一个例外，即面向使用[__自动备份__](https://developer.android.com/guide/topics/data/autobackup)的 Android 6.0（API 级别 23）或更高版本并在其上运行的应用。 此功能默认启用并且会保留应用数据，包括共享首选项（即 Preferences API 使用的内容）   。 可以遵循 Google 的[文档](https://developer.android.com/guide/topics/data/autobackup)禁用此功能。

## <a name="limitations"></a>限制

当存储一个字符串时，此 API 用于存储少量文本。  如果尝试将其用于存储大量文本，则可能会导致性能欠佳。

## <a name="api"></a>API

- [Preferences 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Preferences API 文档](xref:Xamarin.Essentials.Preferences)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Preferences-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
