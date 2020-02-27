---
title: IOS 上的 TabbedPage 半透明选项卡栏
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来设置 TabbedPage 上选项卡栏的半透明度模式。
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: c321884039674d3abb1a4b510ddfe2c062c28211
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646730"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>IOS 上的 TabbedPage 半透明选项卡栏

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于设置[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上的选项卡的半透明度模式。 它通过将 `TabbedPage.TranslucencyMode` 可绑定属性设置为 `TranslucencyMode` 枚举值，在 XAML 中使用：

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

`TabbedPage.On<iOS>` 方法指定此平台特定的仅在 iOS 上运行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中的 `TabbedPage.SetTranslucencyMode` 方法用于通过指定以下 `TranslucencyMode` 枚举值之一来设置[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上选项卡栏的半透明度模式：

- `Default`，它将选项卡栏设置为其默认半透明度模式。 这是 `TabbedPage.TranslucencyMode` 属性的默认值。
- `Transparent`，它将选项卡栏设置为半透明。
- `Opaque`，它将选项卡栏设置为不透明。

此外，`GetTranslucencyMode` 方法可用于检索应用于[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)的 `TranslucencyMode` 枚举的当前值。

结果就是可以设置[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上的选项卡栏的半透明度模式：

![IOS 上半透明和不透明选项卡栏的屏幕截图](tabbedpage-translucent-tabbar-images/translucencymodes.png "透明且不透明的选项卡")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
