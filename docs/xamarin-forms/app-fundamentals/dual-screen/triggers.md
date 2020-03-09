---
title: Xamarin.Forms 双屏触发器
description: 此文章介绍了如何使用 Xamarin.Forms 双屏触发器来响应 XAML 的用户界面更改。
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165526"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Forms 双屏触发器

![](~/media/shared/preview.png "This API is currently pre-release")

[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) 命名空间包含两个状态触发器：

- 在附加布局的视图模式发生改变时，[`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) 会触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。
- 在窗口的视图模式发生改变时，`WindowSpanModeStateTrigger` 会触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。

有关状态触发器的详细信息，请参阅[状态触发器](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)。

## <a name="span-mode-state-trigger"></a>范围模式状态触发器

在附加布局的范围模式发生改变时，[`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) 会触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。 此触发器有以下一个可绑定属性：

- 类型为 [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) 的 [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) 指明应该应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的范围模式。

> [!NOTE]
> 由于 [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) 派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，因此可以将事件处理程序附加到 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下面的 XAML 示例展示了包含 `SpanModeStateTrigger` 对象的 [`Grid`](xref:Xamarin.Forms.Grid)：

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="GridSingle">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridWide">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridTall">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Tall" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Purple" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>
```

此示例在 [`Grid`](xref:Xamarin.Forms.Grid) 对象上设置了可视状态。 在仅显示一个窗格时，`Grid` 的背景色为绿色；在多个窗格并排显示时，背景色为红色；在多个窗格上下显示时，背景色为紫色。

## <a name="window-span-mode-state-trigger"></a>窗口范围模式状态触发器

在窗口的范围模式发生改变时，`WindowSpanModeStateTrigger` 会触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。 此触发器有以下一个可绑定属性：

- 类型为 [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) 的 [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) 指明应该应用 [`VisualState`](xref:Xamarin.Forms.VisualState) 的范围模式。

> [!NOTE]
> 由于 `WindowSpanModeStateTrigger` 派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 类，因此可以将事件处理程序附加到 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下面的 XAML 示例展示了包含 `WindowSpanModeStateTrigger` 对象的 [`Grid`](xref:Xamarin.Forms.Grid)：

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="NotSpanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Spanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
                <VisualState x:Name="Tall">
                    <VisualState.StateTriggers>
                        <dualScreen:WindowSpanModeStateTrigger SpanMode="Tall" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor" Value="Yellow" />
                    </VisualState.Setters>
                </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>    
```

此示例在 [`Grid`](xref:Xamarin.Forms.Grid) 对象上设置了可视状态。 在仅显示一个窗格时，`Grid` 的背景色为红色；在多个窗格并排显示时，背景色为绿色；在多个窗格上下显示时，背景色为黄色。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 视觉对象状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)
