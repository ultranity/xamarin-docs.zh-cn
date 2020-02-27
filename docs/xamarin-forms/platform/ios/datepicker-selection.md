---
title: IOS 上的 DatePicker 项选择
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制何时在 DatePicker 中进行项目选择。
ms.prod: xamarin
ms.assetid: 847E69D1-6AE0-4E82-B9C8-919E009C2014
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: df84cf01909cec564edc9c6c8bb55382a2b9dfe3
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646688"
---
# <a name="datepicker-item-selection-on-ios"></a>IOS 上的 DatePicker 项选择

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定的控件在[`DatePicker`](xref:Xamarin.Forms.DatePicker)中发生项选择时，允许用户指定在浏览控件中的项时或在按下 "**完成**" 按钮后发生项选择。 设置使用在 XAML`DatePicker.UpdateMode`附加属性的值为`UpdateMode`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`DatePicker.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `DatePicker.SetUpdateMode`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制项选择的发生时间、 与`UpdateMode`枚举提供两个可能值：

- `Immediately` – 项选择会出现在用户浏览中的项[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)。 这是在 Xamarin.Forms 中的默认行为。
- `WhenFinished` – 项选择仅发生后用户已按**完成**按钮[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)。

此外，`SetUpdateMode`方法可用于切换的枚举值通过调用`UpdateMode`方法，返回当前`UpdateMode`:

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

结果是，指定`UpdateMode`应用于[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)，它可以控制项选择发生时：

[![DatePicker 更新模式的屏幕截图](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode 特定于平台")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode 特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
