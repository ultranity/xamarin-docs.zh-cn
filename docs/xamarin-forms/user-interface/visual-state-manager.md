---
title: Xamarin. Forms 视觉对象状态管理器
description: 使用可视状态管理器对 XAML 元素按从代码中设置的视觉状态进行更改。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: 0149806f3ab3772bc206cea9540a989d997c817b
ms.sourcegitcommit: f43d5ecafd19cbc5cce39201916a83927a34617a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78214989"
---
# <a name="xamarinforms-visual-state-manager"></a>Xamarin. Forms 视觉对象状态管理器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_使用可视状态管理器根据代码中的可视状态集对 XAML 元素进行更改。_

视觉状态管理器（VSM）提供了一种结构化的方法，可以从代码对用户界面进行可视更改。 在大多数情况下，应用程序的用户界面中 XAML，定义和此 XAML 包含描述可视状态管理器如何影响用户界面的视觉对象的标记。

VSM 引入了_可视状态_的概念。 `Button` 的 Xamarin 视图可以有多个不同的视觉外观，具体取决于其基本状态 &mdash; 是已禁用、已按下还是有输入焦点。 这些是按钮的状态。

可视状态在_视觉状态组_中收集。 可视状态组中的所有可视状态互相排斥。 可视状态和可视状态组由简单的文本字符串标识。

Xamarin. Forms 视觉对象状态管理器定义了一个名为 "CommonStates" 的视觉状态组，其中包含以下可视状态：

- "Normal"
- "已禁用"
- "已设定焦点"
- 选择

派生自[`VisualElement`](xref:Xamarin.Forms.VisualElement)的所有类都支持此视觉对象状态组，这是[`View`](xref:Xamarin.Forms.View)和[`Page`](xref:Xamarin.Forms.Page)的基类。

此外可以定义自己的可视状态组和视觉状态，为这篇文章将演示。

> [!NOTE]
> Xamarin：熟悉[触发器](~/xamarin-forms/app-fundamentals/triggers.md)的窗体开发人员可以知道，触发器还可以根据视图属性中的更改或事件激发来更改用户界面中的视觉对象。 但是，使用触发器来处理这些更改的各种组合可能会变得非常令人困惑。 从历史上看，基于 Windows XAML 的环境，若要缓解的可视状态的组合导致混乱中引入了视觉状态管理器。 利用 VSM，可视状态组中的视觉状态始终是互相排斥的。 在任何时候，每个组中的只有一个状态为当前状态。

## <a name="common-states"></a>常见状态

视觉状态管理器允许您在 XAML 文件中包含标记，如果视图正常、处于禁用状态或具有输入焦点，则可以更改视图的可视外观。 这些_状态称为公共状态_。

例如，假设页面上有一个 `Entry` 视图，并且希望 `Entry` 的视觉外观按以下方式变化：

- 当 `Entry` 处于禁用状态时，`Entry` 应会有粉红色的背景。
- `Entry` 的背景通常为酸橙色。
- 当该 `Entry` 有输入焦点时，应扩展为其正常高度的两倍。

可以将 VSM 标记附加到一个单独的视图，或如果它适用于多个视图可以定义其样式中。 接下来的两部分介绍了这些方法。

### <a name="vsm-markup-on-a-view"></a>在视图上的 VSM 标记

若要将 VSM 标记附加到 `Entry` 视图，请先将 `Entry` 分离到开始标记和结束标记中：

```xaml
<Entry FontSize="18">

</Entry>
```

由于其中一种状态将使用 `FontSize` 属性为 `Entry`中的文本大小加倍，因此提供了一个显式字体大小。

接下来，在这些标记之间插入 `VisualStateManager.VisualStateGroups` 标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)是由[`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager)类定义的附加可绑定属性。 （有关附加的可绑定属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)一文。）这就是 `VisualStateGroups` 属性附加到 `Entry` 对象的方式。

`VisualStateGroups` 属性的类型为[`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList)，它是[`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup)对象的集合。 在 `VisualStateManager.VisualStateGroups` 标记中，为想要包括的每个可视状态组插入一对 `VisualStateGroup` 标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

请注意，`VisualStateGroup` 标记具有指示组名称的 `x:Name` 属性。 `VisualStateGroup` 类定义可改用的 `Name` 属性：

```xaml
<VisualStateGroup Name="CommonStates">
```

可以在同一元素中使用 `x:Name` 或 `Name`，但不能同时使用两者。

`VisualStateGroup` 类定义名为[`States`](xref:Xamarin.Forms.VisualStateGroup.States)的属性，该属性是[`VisualState`](xref:Xamarin.Forms.VisualState)对象的集合。 `States` 是 `VisualStateGroups` 的_content 属性_，因此可以在 `VisualStateGroup` 标记之间直接包含 `VisualState` 标记。 （有关内容属性的介绍，请参见[基本的 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)一文。）

下一步是要包含在该组中的一对每个可视状态的标记。 还可以使用 `x:Name` 或 `Name`来识别这些内容：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` 定义名为[`Setters`](xref:Xamarin.Forms.VisualState.Setters)的属性，该属性是[`Setter`](xref:Xamarin.Forms.Setter)对象的集合。 它们 `Setter` 在[`Style`](xref:Xamarin.Forms.Style)对象中使用的对象。

`Setters`_不_是 `VisualState`的 content 属性，因此需要包含 `Setters` 属性的属性元素标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

你现在可以在每对 `Setters` 标记之间插入一个或多个 `Setter` 对象。 定义前面所述的视觉对象状态的 `Setter` 对象如下：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

每个 `Setter` 标记指示特定属性在该状态为当前状态时的值。 `Setter` 对象引用的任何属性都必须由可绑定的属性支持。

与此类似的标记是 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例程序中 "**查看**" 页的基础。 此页包含三个 `Entry` 视图，但只有第二个视图附加了 VSM 标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

请注意，第二个 `Entry` 的 `Trigger` 集合中还包含一个 `DataTrigger`。 这会导致在第三个 `Entry`中键入内容之前禁用 `Entry`。 以下是在启动 iOS、 Android 和通用 Windows 平台 (UWP) 上运行时的页：

[![视图上的 VSM：已禁用](vsm-images/VsmOnViewDisabled.png "视图上的 VSM-已禁用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

当前视觉状态为 "已禁用"，因此第二个 `Entry` 的背景在 iOS 和 Android 屏幕上为粉红色。 `Entry` 的 UWP 实现不允许在禁用 `Entry` 时设置背景色。

在第三个 `Entry`中输入一些文本后，第二个 `Entry` 会切换为 "正常" 状态，并且背景现在为酸橙色：

[![视图上的 VSM：正常](vsm-images/VsmOnViewNormal.png "视图上的 VSM-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

当你触摸第二个 `Entry`时，它将获取输入焦点。 它将切换到"已设定焦点"状态，并扩展到高度的两倍：

[![视图上的 VSM：聚焦](vsm-images/VsmOnViewFocused.png "基于视图的 VSM")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

请注意，当获得输入焦点时，`Entry` 不会保留酸橙色背景。 可视状态之间切换视觉状态管理器，以前的状态设置的属性是取消设置。 请注意，视觉状态互相排斥。 "正常" 状态并不意味着只启用 `Entry`。 这意味着 `Entry` 已启用，并且没有输入焦点。

如果希望 `Entry` 在 "重点" 状态下具有酸橙色背景，请将另一个 `Setter` 添加到该视觉对象状态：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

为了使这些 `Setter` 对象正常工作，`VisualStateGroup` 必须包含该组中所有状态的 `VisualState` 对象。 如果存在没有任何 `Setter` 对象的视觉状态，请将其包含为空标记：

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>在样式中的视觉状态管理器标记

它通常是共享相同的视觉状态管理器标记在两个或多个视图之间所需的。 在这种情况下，需要将标记放在 `Style` 定义中。

下面是 "查看" "查看" 页面**上**`Entry` 元素的现有隐式 `Style`：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

添加 `VisualStateManager.VisualStateGroups` 附加的可绑定属性的 `Setter` 标记：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

`Setter` 的 content 属性是 `Value`的，因此可以在这些标记中直接指定 `Value` 属性的值。 该属性的类型为 `VisualStateGroupList`：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style>
```

在这些标记中，可以包含一个或多个 `VisualStateGroup` 对象：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

VSM 标记的其余部分是与之前相同。

下面是 "**样式**" 页中显示完整的 vsm 标记的 vsm：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

现在，此页上的所有 `Entry` 视图都以相同的方式对其视觉对象状态做出响应。 另请注意，"重点" 状态现在包含第二个 `Setter`，当每个 `Entry` 具有输入焦点时，还会使每个具有酸橙色背景：

[![VSM 样式](vsm-images/VsmInStyle.png "VSM 样式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>Xamarin 中的可视状态

下表列出了在 Xamarin 中定义的视觉对象状态：

| 类 | 状态 | 更多信息 |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [按钮视觉状态](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [CarouselView 视觉状态](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [ImageButton 视觉状态](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [常见状态](#common-states) |

每个状态都可以通过名为 `CommonStates`的视觉状态组进行访问。

此外，`CollectionView` 还实现了 `Selected` 状态。 有关详细信息，请参阅[更改选定项的颜色](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color)。

## <a name="set-state-on-multiple-elements"></a>设置多个元素的状态

在前面的示例中，可视状态附加到单个元素上并在其上操作。 但是，也可以创建附加到单个元素的可视状态，但这会为同一范围内的其他元素设置属性。 这样就无需在运行状态的每个元素上重复视觉状态。

[`Setter`](xref:Xamarin.Forms.Setter)类型具有一个类型为 `string`的 `TargetName` 属性，该属性表示可视状态的 `Setter` 将操作的目标元素。 定义 `TargetName` 属性时，`Setter` 将 `TargetName` 中定义的元素的 `Property` 设置为 `Value`：

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

在此示例中，名为 `label` 的 `Label` 将其 `TextColor` 属性设置为 "`Red`"。 设置 `TargetName` 属性时，必须在 `Property`中指定属性的完整路径。 因此，若要设置 `Label`上的 `TextColor` 属性，请将 `Property` 指定为 `Label.TextColor`。

> [!NOTE]
> `Setter` 对象引用的任何属性都必须由可绑定的属性支持。

**[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例中的 "**与 Setter TargetName 的 VSM** " 页面说明了如何在单个可视状态组中设置多个元素的状态。 XAML 文件包含一个 `StackLayout`，其中包含 `Label` 元素、`Entry`和 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM 标记附加到 `StackLayout`。 有两个互斥状态，分别名为 "常规" 和 "按下"，每种状态包含 `VisualState` 标记。

当未按下 `Button` 时，"正常" 状态处于活动状态，并且可以输入对问题的响应：

[![VSM Setter TargetName：正常状态](vsm-images/VsmSetterTargetNameNormal.png "VSM 资源库 targetname-正常")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

按下 `Button` 时，"按下" 状态变为活动状态：

[![VSM Setter TargetName：按下状态](vsm-images/VsmSetterTargetNamePressed.png "VSM 资源库 targetname-按下")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

"按下" `VisualState` 指定当 `Button` 处于按下时，其 `Scale` 属性将从默认值1更改为0.8。 此外，名为 `entry` 的 `Entry` 将其 `Text` 属性设置为巴黎。 因此，在 `Button` 按下时，它会略微缩小，`Entry` 显示巴黎。 然后，当释放 `Button` 时，它的默认值为1，`Entry` 显示以前输入的任何文本。

> [!IMPORTANT]
> `Setter` 指定 `TargetName` 属性的元素中当前不支持属性路径。

## <a name="define-your-own-visual-states"></a>定义自己的视觉状态

派生自 `VisualElement` 的每个类都支持通用状态 "常规"、"重点" 和 "已禁用"。 此外，`CollectionView` 类还支持 "Selected" 状态。 在内部， [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)类将在它处于启用或禁用状态、焦点或失去焦点并调用静态[`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String))方法时进行检测：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

这是你将在 `VisualElement` 类中找到的唯一视觉状态管理器代码。 由于每个对象都基于派生自 `VisualElement`的每个类调用 `GoToState`，因此您可以将视觉状态管理器与任何 `VisualElement` 对象一起使用来响应这些更改。

有趣的是，在 `VisualElement`中未显式引用视觉状态组 "CommonStates" 的名称。 组名称不是可视状态管理器的 API 的一部分。 中到目前为止所示的两个示例程序之一，你可以为任何其他内容，从"CommonStates"组的名称和程序仍将起作用。 组名称是仅仅是该组中的状态的一般说明。 隐式理解的任何组中的视觉状态是互相排斥： 一个状态和只能有一个状态是当前在任何时间。

如果要实现自己的视觉状态，需要从代码调用 `VisualStateManager.GoToState`。 通常，你将使此调用的页类代码隐藏文件中。

**[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例中的 " **VSM 验证**" 页显示了如何在连接到输入验证时使用视觉状态管理器。 XAML 文件包含一个包含两个 `Label` 元素、一个 `Entry`和 `Button`的 `StackLayout`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

VSM 标记附加到 `StackLayout` （名为 `stackLayout`）。 有两个互斥状态，分别名为 "有效" 和 "无效"，其中每种状态都包含 `VisualState` 标记。

如果 `Entry` 不包含有效的电话号码，则当前状态为 "无效"，因此 `Entry` 具有粉红色背景，第二个 `Label` 可见，并禁用 `Button`：

[![VSM 验证：无效状态](vsm-images/VsmValidationInvalid.png "VSM 验证-无效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

输入有效的电话号码，然后当前状态将变为"Valid"。 `Entry` 获取一个酸橙色背景，第二个 `Label` 消失，`Button` 现在已启用：

[![VSM 验证：有效状态](vsm-images/VsmValidationValid.png "VSM 验证-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

代码隐藏文件负责处理 `Entry`中的 `TextChanged` 事件。 该处理程序使用的正则表达式来确定输入的字符串是否有效。 名为 `GoToState` 的代码隐藏文件中的方法调用 `stackLayout`的静态 `VisualStateManager.GoToState` 方法：

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

另请注意，从构造函数调用 `GoToState` 方法以初始化状态。 始终应为当前状态。 但不是在代码中有任何引用的可视状态组中，名称虽然它在 XAML 中的引用作为"ValidationStates"为了清晰起见。

请注意，代码隐藏文件只需考虑定义可视状态的页面上的对象，并为该对象调用 `VisualStateManager.GoToState`。 这是因为，这两种可视状态都在页面上定位多个对象。

您可能想知道：代码隐藏文件必须引用定义可视状态的页面上的对象，为什么代码隐藏文件无法直接访问此对象和其他对象？ 当然可以。 但是，使用 VSM 的优点是可以控制如何将视觉元素，对完全在 XAML，将所有 UI 设计保留在一个位置中的不同状态做出反应。 这直接从代码隐藏中访问的可视元素，因此可避免设置可视外观。

## <a name="visual-state-triggers"></a>可视状态触发器

可视状态支持状态触发器，这些触发器是一组专用的触发器，用于定义应用[`VisualState`](xref:Xamarin.Forms.VisualState)应应用的条件。

状态触发器添加到 [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) 的 [`VisualState`](xref:Xamarin.Forms.VisualState) 集合。 此集合可以包含一个或多个状态触发器。 当此集合中的任何状态触发器处于活动状态时，便会应用 [`VisualState`](xref:Xamarin.Forms.VisualState)。

使用状态触发器来控制视觉对象状态时，Xamarin.Forms 使用以下优先规则来确定哪个触发器（以及相应的 [`VisualState`](xref:Xamarin.Forms.VisualState)）处于活动状态：

1. 任何派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 的触发器。
1. 因满足 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) 条件而激活的 [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth)。
1. 因满足 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) 条件而激活的 [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)。

如果多个触发器同时处于活动状态（例如，两个自定义触发器），则标记中声明的第一个触发器优先。

有关状态触发器的详细信息，请参阅[状态触发器](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)。

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>使用视觉对象状态管理器进行自适应布局

可以调整大小在手机上运行的应用程序通常可以查看在纵向或横向纵横比，并且在桌面上运行的 Xamarin.Forms 程序 Xamarin.Forms 采用许多不同的大小和纵横比。 设计良好的应用程序可能会显示不同的方式针对这些不同的页或窗口外观造型其内容。

此方法有时称为_自适应布局_。 由于自适应布局仅涉及程序的视觉对象，它是理想的可视状态管理器应用程序。

一个简单的示例是显示小的会影响应用程序的内容的按钮集合的应用程序。 在纵向模式下，可能会在页面顶部水平行中显示这些按钮：

[![VSM 自适应布局：纵向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 自适应布局-纵向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在横向模式下，可能会移动到一个一侧，和在列中显示的按钮数组：

[![VSM 自适应布局：横向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 自适应布局-横向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

从上到下，通用 Windows 平台、 Android 和 iOS 上运行该程序。

[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)示例中的 " **VSM 自适应布局**" 页面定义名为 "OrientationStates" 的组，其中包含两个名为 "纵向" 和 "横向" 的可视状态。 （更复杂的方法可能会基于多个不同的页或窗口宽度。）

VSM 标记出现在 XAML 文件中的四个位置。 名为 `mainStack` 的 `StackLayout` 包含菜单和内容，这是 `Image` 元素。 此 `StackLayout` 在纵向模式下应具有垂直方向，在横向模式下为水平方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">
                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">
                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">
                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>
                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

名为 `menuScroll` 的内部 `ScrollView` 和名为 `menuStack` 的 `StackLayout` 实现按钮菜单。 这些布局的方向相对 `mainStack`。 在纵向模式下水平和垂直在横向模式下，应为菜单。

中的按钮本身的隐式样式是 VSM 标记的第四个部分。 此标记设置特定于纵向和横向方向 `VerticalOptions`、`HorizontalOptions`和 `Margin` 属性。

代码隐藏文件设置 `menuStack` 的 `BindingContext` 属性以实现 `Button` 命令，同时将处理程序附加到页面的 `SizeChanged` 事件：

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

`SizeChanged` 处理程序对两个 `StackLayout` 和 `ScrollView` 元素调用 `VisualStateManager.GoToState`，然后遍历 `menuStack` 的子级，以调用 `VisualStateManager.GoToState` 元素的 `Button`。

这可能看起来像代码隐藏文件可以处理更直接地通过在 XAML 文件中，设置元素的属性的方向更改，但视觉状态管理器肯定是一种更加结构化的方法。 所有视觉对象将保留在 XAML 文件，其中它们变得更轻松地检查，维护和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>使用 Xamarin.University 视觉状态管理器

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin 3.0 视觉对象状态管理器视频**

## <a name="related-links"></a>相关链接

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [状态触发器](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
