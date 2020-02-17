---
title: Xamarin.Forms DualScreenHelper
description: 本指南介绍了如何使用 Xamarin.Forms DualScreenHelper 来优化 Surface Duo 和 Surface Neo 等双屏设备的应用体验。
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: b06e105560de635a21b42e8366bb47842372cf6a
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145541"
---
# <a name="xamarinforms-dualscreenhelper"></a>Xamarin.Forms DualScreenHelper
[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)
_DualScreenHelper 可用于检测某设备是否支持画中画模式，然后使你能够在图片窗口中以图片的形式打开 ContentPage。如果你在 Neo 设备上运行，则在撰写模式下，这将在 WonderBar 中打开页面。_
## <a name="properties"></a>属性
- `HasCompactModeSupport`：用于检查平台是否支持在 CompactMode 中打开 ContentPage。
- `OpenCompactMode`：如果平台支持此属性，则将在 CompactMode 中打开 ContentPage。
## <a name="example-usage"></a>示例用法
```c#
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if(!DualScreenHelper.HasCompactModeSupport())
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