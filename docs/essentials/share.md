---
title: Xamarin.Essentials:共享
description: Xamarin.Essentials 中的 Share 类使应用程序能够将数据（例如文本和 Web 链接）共享到设备上的其他应用程序。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
ms.openlocfilehash: f964967dce0dbb7e49d52a7f865b0fe8a9957bbe
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545198"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials:共享

Share 类使应用程序能够将数据（例如文本和 Web 链接）共享到设备上的其他应用程序  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>使用 Share

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用具有数据请求有效负载的 `RequestAsync` 方法来使用 Share 功能，此有效负载包括要共享到其他应用程序的信息。 文本和 URI 可以混合使用，每个平台都将根据内容进行筛选处理。

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

在进行请求时显示的共享到外部应用程序的用户界面：

![共享](images/share.png)

## <a name="files"></a>文件

借助此功能，应用可以在设备上与其他应用程序共享文件。 Xamarin.Essentials 将自动检测文件类型 (MIME) 并请求进行共享。 每个平台可能只支持特定文件扩展名。

以下是将文本写入磁盘并将其与其他应用共享的示例：

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="presentation-location"></a>演示位置

在 iPadOS 上请求共享时，可以在弹窗控件中演示。 可以使用 `PresentationSourceBounds` 属性指定位置：

```csharp
await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

## <a name="platform-differences"></a>平台差异

# <a name="android"></a>[Android](#tab/android)

- `Subject` 属性用于所需的消息主题。

# <a name="ios"></a>[iOS](#tab/ios)

- `Subject` 未使用。
- `Title` 未使用。

# <a name="uwp"></a>[UWP](#tab/uwp)

- 如果未设置，`Title` 将默认为应用程序名称。
- `Subject` 未使用。

-----

## <a name="api"></a>API

- [Share 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Share API 文档](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
