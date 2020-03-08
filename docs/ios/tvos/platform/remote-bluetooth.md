---
title: 适用于 Xamarin 中 tvOS 的 Siri 远程和蓝牙控制器
description: 本文介绍如何在使用 Xamarin 编写的 tvOS 应用中使用 Siri 远程和蓝牙游戏控制器。
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c0338fce694d61dc19484c56dbc00bb854d0d0d7
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915804"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>适用于 Xamarin 中 tvOS 的 Siri 远程和蓝牙控制器

你的 tvOS 应用程序的用户将不会与 iOS 直接交互，因为在 iOS 中点击设备上的图像，而不是使用[Siri 远程](#The-Siri-Remote)在房间内间接显示。

如果你的应用程序是一项游戏，你可以根据需要为你的应用中的 iOS （MFI）[蓝牙游戏控制器](#Bluetooth-Game-Controllers)创建第三方支持。

[![](remote-bluetooth-images/intro01.png "The Bluetooth Remote and Game Controller")](remote-bluetooth-images/intro01.png#lightbox)

本文介绍了[Siri 远程](#The-Siri-Remote)、[触控 Surface 手势](#Touch-Surface-Gestures)和[Siri 遥控器](#Siri-Remote-Buttons)，并演示了如何通过[笔势和情节提要](#Gestures-and-Storyboards)、[笔势和代码](#Gestures-and-Code)以及[低级别事件处理](#Low-Level-Event-Handling)来处理它们。 最后介绍了如何在 tvOS 应用程序中使用[游戏控制器](#Working-with-Game-Controllers)。

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri 远程

用户与 Apple TV 和 Xamarin tvOS 应用交互的主要方式是通过包含的 Siri 远程。 Apple 设计的遥控器用于弥补沙发上用户所在的距离，以及在电视屏幕上的房间内显示的 Apple TV 用户界面之间的距离。

作为 tvOS 应用开发人员，您的挑战是创建一个快速、易于使用且视觉引人注目的用户界面，该界面利用 Siri 遥控器的触摸面、加速感应、陀螺仪和按钮。

[![](remote-bluetooth-images/remote01.png "The Siri Remote")](remote-bluetooth-images/remote01.png#lightbox)

Siri 远程在 tvOS 应用程序中具有以下功能和预期用法：

|Feature|一般应用使用情况|游戏应用使用情况|
|---|---|---|
|**触摸表面**<br />轻扫以导航，按选择并按住上下文菜单。|**点击/刷**<br />可设定焦点项之间的 UI 导航。<br /><br />**单击“网络和共享中心”**<br />激活选定的（焦点）项。|**点击/刷**<br />取决于游戏设计，并可通过点击边缘来用作一种3-D 板。<br /><br />**单击“网络和共享中心”**<br />执行主按钮函数。|
|**菜单**<br />按返回上一个屏幕或菜单。|返回到上一个屏幕，从主应用屏幕退出到 Apple TV 主屏幕。|暂停和恢复游戏，返回到上一屏幕并从主应用屏幕退出到 Apple TV 主屏幕。|
|**Siri/搜索**<br />在带有 Siri 的国家/地区，在所有其他国家/地区按下并保持语音控制，显示搜索屏幕。|不适用|不适用|
|**播放/暂停**<br />播放和暂停媒体，或在应用中提供辅助功能。|开始播放媒体和暂停/恢复播放。|执行辅助按钮功能，或跳过简介视频（如果存在）。|
|**主页**<br />按返回主屏幕，双击以显示正在运行的应用程序，按下并保持进入睡眠状态。|不适用|不适用|
|**数据量**<br />控制附加音频/视频设备量。|不适用|不适用|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>触摸 Surface 手势

Siri 远程的触摸图面能够检测到你可以在 tvOS 应用程序中响应的各种单指手势：

|轻扫|单击|点击|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|在屏幕上的 UI 元素之间移动选定内容（焦点）（向上、向下、向右）。 轻扫可用于使用惯性快速滚动大列表内容。|激活选定的（焦点）项，或充当游戏中的主要按钮。 单击并按住可激活上下文菜单或辅助功能。|轻轻地点击边缘上的触摸图面可像在一屏上方向按钮一样，向上、向下、向左或向右移动，具体取决于点击的区域。 根据应用，可以使用来显示隐藏的控件。|

Apple 提供了以下有关使用触摸 Surface 势的建议：

- **区分单击和点击**单击是用户的一项有意操作，非常适合于选择、激活和游戏的主要按钮。 点击更微妙，应慎用，因为用户经常会 Siri 遥控器，并可能会意外激活点击事件。
- **不要重新定义标准笔势**-用户期望特定手势执行特定操作时，不应在应用中重新定义这些笔势的含义或函数。 一个例外是活动游戏过程中的游戏应用。
- 更**慎重地定义新的手势**，用户会希望特定手势执行特定操作。 应避免定义用于执行标准操作的自定义手势。 同样，游戏最常见的例外是，自定义手势可以向游戏中添加有趣的沉浸式游戏。
- **如果合适，请**在触摸板的角部上点击 "触摸"，轻轻点击 "触摸" 表面的角部，就像某个游戏控制器上的一个 d 板，将焦点或方向向上、向下、向左或向右移动。 如果需要，您应该在您的应用程序或游戏中响应这些手势。

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri 远程按钮

除了触摸图面上的手势外，应用还可以响应用户单击触摸表面或按播放/暂停按钮。 如果要使用游戏控制器框架访问 Siri 远程，还可以检测到按下的菜单按钮。

此外，可以通过使用带有标准 `UIKit` 元素的笔势识别器来检测菜单按钮按下。 如果你截获按下的菜单按钮，你将负责关闭当前视图和视图控制器并返回到上一个视图控制器。

> [!IMPORTANT]
> 应**始终**将函数分配到远程上的播放/暂停按钮。 具有 "无法正常工作" 按钮会使你的应用程序看起来对最终用户中断。 如果没有此按钮的有效函数，请将相同的函数指定为 "主要" 按钮（单击 "触摸表面"）。

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>手势和情节提要

在 tvOS 应用中使用 Siri 远程的最简单方法是将笔势识别器添加到接口设计器中的视图。

若要添加手势识别器，请执行以下操作：

1. 在**解决方案资源管理器**中，双击 `Main.storyboard` 文件，然后将其打开以便编辑接口设计器。
2. 从**库**中拖放一个 "拖动"**手势识别器**，并将其放在视图上：

    [![](remote-bluetooth-images/storyboard01.png "A Tap Gesture Recognizer")](remote-bluetooth-images/storyboard01.png#lightbox)
3. 选中 "**属性检查器**" 的 "**按钮**" 部分中的 "**选择**"：

    [![](remote-bluetooth-images/storyboard02.png "Check Select")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **选择**表示该笔势将响应用户单击 Siri 遥控器上的**触摸表面**。 你还可以选择响应**菜单**、**播放/暂停**、**向上**、**向下**、向**左**和**向右**按钮。
5. 接下来，请从**点击手势识别器**向上绑定一个**操作**，并将其调用 `TouchSurfaceClicked`：

    [![](remote-bluetooth-images/storyboard03.png "An Action from the Tap Gesture Recognizer")](remote-bluetooth-images/storyboard03.png#lightbox)
6. 保存更改并返回到 Visual Studio for Mac。

编辑视图控制器（示例 `FirstViewController.cs`）文件，并添加以下代码以处理触发的手势：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

有关使用情节提要的详细信息，请参阅[tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 具体而言，就是[创建用户界面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface)，并用["输出口" 和 "操作" 部分编写代码](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)。

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>手势和代码

还可以选择直接在代码中C#创建笔势，并将其添加到用户界面中的视图。 例如，若要添加一系列滑动手势识别器，请编辑视图控制器并添加以下代码：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>低级别事件处理

如果要基于 tvOS 应用中 `UIKit` 创建自定义类型（如 `UIView`），则还可以通过 `UIPress` 事件提供按钮按下的低级处理。

`UIPress` 事件是为了 tvOS `UITouch` 事件对 iOS 的作用，但 `UIPress` 会返回有关 Siri 远程或其他附加蓝牙设备（如游戏控制器）的按钮的信息。 `UIPress` 事件描述按下的按钮及其状态（开始、取消、更改或结束）。

对于蓝牙游戏控制器等设备上的模拟按钮，`UIPress` 还会返回应用到按钮的强制量。 `UIPress` 事件的 `Type` 属性定义已更改状态的物理按钮，而其余属性则描述发生的更改。

下面的代码演示了处理 `UIView`的低级 `UIPress` 事件的示例：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

与 `UITouch` 事件一样，如果需要实现任何 `UIPress` 事件重写，则应实现所有这四种重写。

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>蓝牙游戏控制器

除了 Apple TV 第三方随附的标准 Siri 远程（MFI）蓝牙游戏控制器外，还可以与 Apple TV 配对，并用于控制 tvOS 应用。

[![](remote-bluetooth-images/game01.png "Bluetooth Game Controllers")](remote-bluetooth-images/game01.png#lightbox)

游戏控制器可用于增强游戏，并在游戏中提供浸入式。 它们还可用于控制标准 Apple TV 接口，因此使用不必在远程控制器和控制器之间切换。

> [!IMPORTANT]
> 蓝牙游戏控制器是最终用户可能会进行的可选购买，你的应用程序无法强制用户购买一个。 如果你的应用程序支持游戏控制器，则它还必须支持 Siri 远程，以便所有 Apple TV 用户都可以使用该游戏。

游戏控制器具有以下功能和 tvOS 应用中预期的用法：

|Feature|一般应用使用情况|游戏应用使用情况|
|---|---|---|
|**方向键**|在 UI 元素中导航（更改焦点）。|依赖于游戏。|
|**指向**|激活选定的（焦点）项。|执行主按钮功能并确认对话框操作。|
|**B**|返回到上一个屏幕，或在应用程序的主屏幕上退出主屏幕。|执行辅助按钮函数或返回到上一屏幕。|
|**X**|开始播放媒体，或暂停/恢复播放。|依赖于游戏。|
|**是**|不适用|依赖于游戏。|
|**菜单**|返回到上一个屏幕，或在应用程序的主屏幕上退出主屏幕。|暂停/恢复游戏，返回到上一屏幕或在应用程序的主屏幕上退出主屏幕。|
|**左肩按钮**|向左导航。|依赖于游戏。|
|**左触发器**|向左导航。|依赖于游戏。|
|**右肩按钮**|向右导航。|依赖于游戏。|
|**右触发器**|向右导航|依赖于游戏。|
|**左操纵杆**|在 UI 元素中导航（更改焦点）。|依赖于游戏。|
|**右操纵杆**|不适用|依赖于游戏。|

Apple 提供了以下有关使用游戏控制器的建议：

- **确认游戏控制器连接**-最终用户可以随时启动和停止 tvOS 应用。 在应用程序启动或唤醒时，应始终检查游戏控制器的状态，并根据需要采取措施。
- **确保你的应用在 Siri 远程和游戏控制器上均可工作**-不要求用户在 Siri 远程和游戏控制器之间切换即可使用你的应用程序。 经常测试您的应用程序，同时使用这两种类型的控制器确保一切都易于导航并按预期方式工作。
- **提供一种方法**，按下菜单按钮时，应始终返回到上一屏幕。 如果用户位于主应用屏幕上，则菜单按钮会将其返回到 Apple TV 主屏幕。 在游戏期间，菜单按钮应显示一则警报，使用户能够暂停/恢复游戏或返回主菜单。

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>使用游戏控制器

如上所述，除了 Apple TV 随附的标准 Siri 遥控器外，用户还可以选择附加适用于 iOS （MFI）蓝牙游戏控制器的第三方，并使用它来控制 tvOS 应用。

如果你的应用程序需要低级别控制器输入，则可以使用 Apple 的[游戏控制器框架](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)，该框架对 tvOS 进行了以下修改：

- 已将微游戏控制器配置文件（`GCMicroGamepad`）添加到 Siri 远程。
- 新的 `GCEventViewController` 类可用于通过应用路由游戏控制器事件。 有关更多详细信息，请参阅下面的[确定游戏控制器输入](#determining-game-controller-input)部分。

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>游戏控制器支持要求

如果你的 tvOS 应用支持游戏控制器，Apple 具有几个具体的要求：

- **必须支持 Siri 远程**-必须始终支持 Siri 远程。 游戏不能需要第三方游戏控制器才能播放。
- **必须支持扩展控制布局**-所有 TvOS 游戏控制器均为非 formfitting 的扩展控制器。
- **游戏必须可通过独立控制器进行播放**-如果你的应用程序支持扩展游戏控制器，则必须仅在该游戏控制器上进行播放。
- **必须支持 "播放/暂停" 按钮**-在游戏期间，如果用户按 "播放/暂停" 按钮，则应显示一则通知，让用户能够暂停/恢复游戏或返回到主菜单。

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>启用游戏控制器支持

若要在 tvOS 应用中启用游戏控制器支持，请在**解决方案资源管理器**中双击 `Info.plist` 文件，将其打开进行编辑：

[![](remote-bluetooth-images/game02.png "The Info.plist editor")](remote-bluetooth-images/game02.png#lightbox)

在 "**游戏控制器**" 部分下，选中 "**启用游戏控制器**"，然后检查应用将支持的所有游戏控制器类型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>使用 Siri 远程作为游戏控制器

Apple TV 附带的 Siri 远程可用作受限游戏控制器。 与其他游戏控制器一样，它作为 `GCController` 对象显示在游戏控制器框架中，同时支持 `GCMotion` 和 `GCMicroGamepad` 配置文件。

用作游戏控制器时，Siri 远程具有以下特征：

- 可将触摸图面用作提供模拟输入数据的 D 板。
- 远程可以在纵向或横向方向上使用，应用决定配置文件对象是否应自动翻转输入数据。
- 单击触摸图面类似于在游戏控制器上按按钮**A** 。
- "播放/暂停" 按钮的作用类似于游戏控制器上的按钮**X** 。
- 菜单按钮应显示一则警报，使用户能够暂停/恢复游戏或返回主菜单。

<a name="Summary" />

### <a name="determining-game-controller-input"></a>确定游戏控制器输入

与可以与触控事件并行接收游戏控制器事件的 iOS 不同，tvOS 处理所有低级别事件以提供高级别 `UIKit` 事件。 因此，如果需要访问低级别游戏控制器事件，则需要关闭 `UIKit`的默认行为。

在 tvOS 上，当你想要直接处理游戏控制器输入时，需要使用 `GCEventViewController` （或子类）来显示游戏的用户界面。 每次 `GCEventViewController`*第一个响应*程序时，都会通过游戏控制器框架捕获游戏控制器输入并将其传递到应用程序。

您可以使用 `GCEventViewController` 类的 `UserInteractionEnabled` 属性来切换事件的处理和处理方式。

有关实施游戏控制器支持的信息，请参阅 tvOS 和[游戏控制器](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)指南[的应用程序编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)中的 Apple 使用[游戏控制器](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)部分。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了 Apple TV、触控 Surface 手势和 Siri 遥控器随附的新 Siri 遥控器。 接下来，它涉及到使用笔势和情节提要、笔势和代码以及低级别事件。 最后，如果讨论了如何使用游戏控制器。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
