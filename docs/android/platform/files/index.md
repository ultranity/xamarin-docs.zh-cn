---
title: 使用 Xamarin 进行文件访问
description: 本指南将说明 Xamarin 中的文件访问
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914232"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>使用 Xamarin 进行文件存储和访问

Android 应用程序的常见要求是操作文件 &ndash; 保存图片、下载文档或导出与其他程序共享的数据。 Android （基于 Linux）通过为文件存储提供空间来支持这种情况。 Android 将文件系统分组到两种不同类型的存储中：

* **内部存储**&ndash; 这是只能由应用程序或操作系统访问的文件系统的一部分。
* **外部存储**&ndash; 这是文件存储的分区，所有应用、用户和其他设备都可以访问这些文件。 在某些设备上，外部存储可以是可移动存储（例如 SD 卡）。

这些分组只是概念性的，不一定指设备上的单个分区或目录。 Android 设备将始终提供内部存储和外部存储的分区。 某些设备可能有多个被视为外部存储的分区。 无论是哪个分区，用于读取、写入或创建文件的 Api 都是相同的。 Xamarin Android 应用程序可使用两组 Api 来访问文件：

1. **.Net api （由 Mono 提供，由 xamarin 进行包装）** &ndash; 它们包括[Xamarin](~/essentials/index.md?context=xamarin/android)提供的[文件系统帮助](~/essentials/file-system-helpers.md?context=xamarin/android)程序。 .NET Api 提供最佳的跨平台兼容性，因此本指南的重点是这些 Api。
1. **本机 java 文件访问 api （由 java 提供，由 Xamarin &ndash; 包装）** Java 提供自己的 api 用于读取和写入文件。 这些是 .NET Api 的完全可接受的替代项，但特定于 Android，不适用于打算跨平台的应用。

在 Xamarin 中，读取和写入文件几乎完全相同，因为它是任何其他 .NET 应用程序。 Xamarin Android 应用确定将操作的文件的路径，然后使用标准 .NET 惯例进行文件访问。 由于与设备到设备或从 Android 版本到 Android 版本相比，内部和外部存储的实际路径可能有所不同，因此不建议对文件路径进行硬编码。 请改用 Xamarin Api 来确定文件的路径。 这样，用于读取和写入文件的 .NET Api 将公开本机 Android Api，这有助于确定内部和外部存储上的文件的路径。

在讨论文件访问涉及的 Api 之前，了解内部和外部存储的一些细节很重要。 下一节将对此进行讨论。

## <a name="internal-vs-external-storage"></a>内部和外部存储

从概念上讲，内部存储和外部存储非常相似 &ndash; 它们都是 Xamarin Android 应用程序可以保存文件的位置。 对于不熟悉 Android 的开发人员来说，这种相似性可能会令人感到困惑，因为当应用程序应使用内部存储和外部存储时，这一点并不清楚。

内部存储是指 Android 分配给操作系统的非易失性内存，Apk，适用于单个应用。 此空间不可访问，但操作系统或应用除外。 Android 将在每个应用的内部存储分区中分配一个目录。 卸载该应用后，在该目录中的内部存储上保存的所有文件也会被删除。 内部存储最适合仅适用于应用程序的文件，并且在卸载应用程序后不会与其他应用程序共享。 在 Android 6.0 或更高版本上，使用[android 6.0 中的自动备份功能](https://developer.android.com/guide/topics/data/autobackup)，Google 可能会自动备份内部存储上的文件。 内部存储具有以下缺点：

* 无法共享文件。
* 卸载应用程序时，将删除这些文件。
* 内部存储上的可用空间可能会受到限制。

外部存储指的是不是内部存储且不能以独占方式访问应用的文件存储。 外部存储的主要目的是提供一个位置，用于放置要在应用之间共享的文件，或者要在内部存储上容纳太大的文件。 外部存储的优点是它的文件的空间通常比内部存储要大得多。 但是，外部存储并非始终保证存在于设备上，可能需要用户提供的特殊权限才能访问它。

> [!NOTE]
> 对于支持多个用户的设备，Android 将为每个用户提供自己在内部和外部存储中的目录。 设备上的其他用户无法访问此目录。 这种分离对应用程序不可见，只要它们不对内部或外部存储上的文件的路径进行硬编码。

作为经验法则，Xamarin 应用程序应在合理的情况下将其文件保存在内部存储上，并依赖于外部存储（当文件需要与其他应用共享时），这是非常大的，或者即使应用程序已卸载也应保留。 例如，配置文件最适用于内部存储，因为它没有用于创建它的应用的重要性。  与此相反，照片非常适合用于外部存储。 它们可能会很大，并且在许多情况下，用户可能会出于共享方式进行共享或访问，即使卸载应用程序也是如此。

本指南将重点介绍内部存储。 有关使用 Xamarin Android 应用程序中的外部存储的详细信息，请参阅本指南[外部存储](~/android/platform/files/external-storage.md)。

## <a name="working-with-internal-storage"></a>使用内部存储

应用程序的内部存储目录由操作系统确定，并由 `Android.Content.Context.FilesDir` 属性公开给 Android 应用。 这会返回一个 `Java.IO.File` 对象，该对象表示 Android 专用于应用的目录。  例如，具有包名称 **.com**的应用程序内部存储目录可能为：

```bash
/data/user/0/com.companyname/files
```

本文档将内部存储目录称为_内部\_存储_。

> [!IMPORTANT]
> 内部存储目录的准确路径可能因设备而异，并且不同于 Android 版本。 因此，应用程序不能对内部文件存储目录的路径进行硬编码，而是使用 Xamarin Api，如 `System.Environment.GetFolderPath()`。

若要最大程度地共享代码，Xamarin Android 应用（或面向 Xamarin 的 Xamarin 应用程序应用）应使用[`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*)方法。 在 Xamarin 中，此方法将返回与 `Android.Content.Context.FilesDir`位置相同的目录的字符串。 此方法采用枚举（`System.Environment.SpecialFolder`）来标识一组枚举常数，这些常数表示操作系统使用的特殊文件夹的路径。 并非所有 `System.Environment.SpecialFolder` 值都将映射到 Xamarin 上的有效目录。 下表描述了给定 `System.Environment.SpecialFolder`值的预期路径：

| System.Environment.SpecialFolder | 路径  |
|----------------------|---|
| `ApplicationData` | **_内部\_存储_/.config** |
| `Desktop` | **_内部\_存储_/Desktop** |
| `LocalApplicationData` | **_内部\_存储_/.local/share** |
| `MyDocuments` | **_内部\_存储_** |
| `MyMusic` | **_内部\_存储_/Music** |
| `MyPictures` | **_内部\_存储_/Pictures** |
| `MyVideos` | **_内部\_存储_/Videos** |
| `Personal` | **_内部\_存储_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>在内部存储上读取或写入文件

[ C#用于写入](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file)文件的任何 api 都足以实现;这一切都是获取分配给应用程序的目录中的文件的路径。 强烈建议使用异步版本的 .NET Api 来最大程度地减少可能与阻止主线程的文件访问相关的任何问题。

此代码片段是将一个整数写入 UTF-8 文本文件到应用程序内部存储目录的一个示例：

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

下一个代码片段提供了一种方法来读取存储在文本文件中的整数值：

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>使用 Xamarin &ndash; 文件系统帮助程序

[Xamarin](~/essentials/file-system-helpers.md?context=xamarin/android)是一组 api，用于编写跨平台兼容的代码。 [文件系统帮助](~/essentials/file-system-helpers.md?context=xamarin/android)程序是一个类，它包含一系列帮助程序，用于简化应用程序的缓存和数据目录的查找。 此代码片段提供了一个示例，演示如何查找应用的内部存储目录和缓存目录：

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>隐藏 `MediaStore` 中的文件

`MediaStore` 是一种 Android 组件，用于收集有关 Android 设备上的媒体文件（视频、音乐、图像）的元数据。 它的目的是简化在设备上跨所有 Android 应用共享这些文件。

专用文件不会显示为可共享的媒体。 例如，如果应用将图片保存到其专用外部存储中，则媒体扫描程序（`MediaStore`）将不会提取该文件。

`MediaStore`将选取公共文件。 具有零字节文件名称的目录 **。** `MediaStore`不会对 NOMEDIA 进行扫描。

## <a name="related-links"></a>相关链接

* [外部存储](~/android/platform/files/external-storage.md)
* [在设备存储上保存文件](https://developer.android.com/training/data-storage/files)
* [Xamarin Essentials 文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)
* [利用自动备份来备份用户数据](https://developer.android.com/guide/topics/data/autobackup)
* [Adoptable 存储](https://source.android.com/devices/storage/adoptable)
