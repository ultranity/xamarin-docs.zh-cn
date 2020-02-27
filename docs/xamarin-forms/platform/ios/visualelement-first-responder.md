---
title: IOS 上的 VisualElement 第一个响应程序
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的，使 VisualElement 对象成为触控事件的第一个响应方。
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: be6c233b63d172d2fcacb1cea7f5e9aeeb7faed1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646712"
---
# <a name="visualelement-first-responder-on-ios"></a>IOS 上的 VisualElement 第一个响应程序

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 特定于 iOS 平台使[`VisualElement`](xref:Xamarin.Forms.VisualElement)对象成为触控事件的第一个响应方，而不是包含该元素的页。 它通过将 `VisualElement.CanBecomeFirstResponder` 可绑定属性设置为 `true`在 XAML 中使用：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

`VisualElement.On<iOS>` 方法指定此平台特定的仅在 iOS 上运行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中的 `VisualElement.SetCanBecomeFirstResponder` 方法用于将 `VisualElement` 设置为触控事件的第一个响应方。 此外，`VisualElement.CanBecomeFirstResponder` 方法可用于返回 `VisualElement` 是否为触控事件的第一个响应方。

结果是， [`VisualElement`](xref:Xamarin.Forms.VisualElement)可以成为触控事件的第一个响应方，而不是包含该元素的页。 这样，聊天应用程序就可以在点击[`Button`](xref:Xamarin.Forms.Button)时关闭键盘。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
