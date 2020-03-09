---
title: Xamarin.Forms 双屏平台帮助程序
description: 本指南介绍了如何使用 Xamarin.Forms DualScreenHelper 来来优化 Surface Duo 和 Surface Neo 等双屏设备的应用体验。
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: e16e926d9eb3c5f3ca04b7e1359f24624a971617
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160564"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Forms 双屏平台帮助程序

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)

`DualScreenHelper` 类可用于检测某设备是否支持画中画模式，然后使你能够在图片窗口中以图片的形式打开 `ContentPage`。 如果你在 Neo 设备上运行，则在撰写模式下，这将在 WonderBar 中打开页面。

## <a name="properties"></a>属性

- `HasCompactModeSupport` 可用于检查平台是否支持在 CompactMode 中打开 `ContentPage`。
- 如果平台支持，`OpenCompactMode` 将在 CompactMode 中打开 `ContentPage`。

## <a name="example"></a>示例

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
