---
title: watchOS 故障排除
description: 本文档介绍了通过 Xamarin 进行 watchOS 开发的已知问题和解决方法。 它介绍了有问题的图像、手动添加接口控制器文件、从命令行启动监视应用程序等。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 06524163fadc4300d55ec90f35723fd1561bb8a0
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915625"
---
# <a name="watchos-troubleshooting"></a>watchOS 故障排除

本页包含可能遇到的问题的其他信息和解决方法。

- [已知问题](#knownissues)

- [从图标图像中删除 Alpha 通道](#noalpha)

- [手动添加 Xcode Interface Builder 的接口控制器文件](#add)。

- [从命令行启动 WatchApp](#command_line)。

<a name="knownissues" />

## <a name="known-issues"></a>已知问题

### <a name="general"></a>常规

<a name="deploy" />

- 较早版本的 Visual Studio for Mac 错误地将**AppleCompanionSettings**图标之一显示为88x88 像素;如果尝试提交到 App Store，则会导致**缺少图标错误**。
    此图标应为87x87 像素（ **@3x** Retina 屏幕的29个单位）。 无法在 Visual Studio for Mac 中解决此问题-请在 Xcode 中编辑图像资产，或者手动编辑**内容 json**文件。

- 如果未[正确设置](~/ios/watchos/get-started/project-references.md)监视扩展项目的**Info.plist > WKApp 捆绑 Id**与 WATCH 应用的**捆绑 id**相匹配，则调试器将无法连接，并且 Visual Studio for Mac 将等待消息 *"等待调试程序连接"* 。

- 在**通知**模式下支持调试，但可能不可靠。 重试有时会起作用。 确认监视应用的**info.plist** `WKCompanionAppBundleIdentifier` 设置为与 iOS 父/容器应用（即 iPhone 上运行的应用程序）的捆绑标识符相匹配。

- iOS 设计器不显示用于速览或通知界面控制器的入口点箭头。

- 不能将两个 `WKNotificationControllers` 添加到情节提要。
    解决方法： storyboard XML 中的 `notificationCategory` 元素始终以相同的 `id`插入。 若要解决此问题，你可以添加两个（或多个）通知控制器，在文本编辑器中打开情节提要文件，然后手动将 `id` 元素更改为唯一。

    [![](troubleshooting-images/duplicate-id-sml.png "Opening the storyboard file in a text editor and manually change the id element to be unique")](troubleshooting-images/duplicate-id.png#lightbox)

- 尝试启动应用程序时，可能会看到错误 "尚未生成应用程序"。 如果启动项目设置为监视扩展项目，则会在**清除**后出现这种情况。
    解决方法是选择 "**生成" > "全部重新生成**"，然后重新启动应用。

### <a name="visual-studio"></a>Visual Studio

对于 Watch 工具包，iOS 设计器支持*要求*正确配置解决方案。 如果未设置项目引用（请参阅[如何设置引用](~/ios/watchos/get-started/project-references.md)），则设计图面将不能正常工作。

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>从图标图像中删除 Alpha 通道

图标不应包含 alpha 通道（alpha 通道用于定义图像的透明区域），否则应用程序存储提交过程中将拒绝应用，错误如下：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

可以轻松地使用**预览版**应用删除 Mac OS X 上的 alpha 通道：

1. 在**预览**中打开图标图像，然后选择 "**文件" > 导出**。

2. 如果出现 alpha 通道，则显示的对话框将包括**alpha**复选框。

    ![](troubleshooting-images/remove-alpha-sml.png "The dialog that appears will include an Alpha checkbox if an alpha channel is present")

3. *Untick* " **Alpha** " 复选框，并将文件**保存**到正确的位置。

4. 图标图像现在应传递 Apple 的验证检查。

<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>手动添加接口控制器文件

> [!IMPORTANT]
> Xamarin 的 WatchKit 支持包括在 iOS 设计器中设计 watch 情节提要（在 Visual Studio for Mac 和 Visual Studio 中），这不需要下述步骤。 只需在 "Visual Studio for Mac 属性" pad 中为接口控制器指定类名称C# ，即可自动创建代码文件。

*如果*你使用的是 Xcode Interface Builder，请按照以下步骤为你的手表应用创建新的界面控制器并启用与 Xcode 的同步，以便可以在中C#使用插座和操作：

1. 在 Xcode 中打开 "监视" 应用的**界面。情节提要** **Interface Builder**。

    ![](troubleshooting-images/add-6.png "Opening the storyboard in Xcode Interface Builder")

2. 将新 `InterfaceController` 拖动到情节提要上：

    ![](troubleshooting-images/add-1.png "A InterfaceController")

3. 你现在可以将控件拖到接口控制器上（例如 标签和按钮），但你仍无法创建插座或操作，因为不存在 **.h**头文件。 以下步骤将导致创建所需的 **.h**头文件。

    ![](troubleshooting-images/add-2.png "A button in the layout")

4. 关闭情节提要并返回到 Visual Studio for Mac。 在 " C# **监视应用扩展**" 项目中创建新文件**MyInterfaceController.cs** （或任何所需的名称）（而不是情节提要的 "监视" 应用本身）。 添加下面的代码（更新命名空间、classname 和构造函数名称）：

    ```csharp
    using System;
    using WatchKit;
    using Foundation;

    namespace WatchAppExtension  // remember to update this
    {
        public partial class MyInterfaceController // remember to update this
        : WKInterfaceController
        {
            public MyInterfaceController // remember to update this
            (IntPtr handle) : base (handle)
            {
            }
            public override void Awake (NSObject context)
            {
                base.Awake (context);
                // Configure interface objects here.
                Console.WriteLine ("{0} awake with context", this);
            }
            public override void WillActivate ()
            {
                // This method is called when the watch view controller is about to be visible to the user.
                Console.WriteLine ("{0} will activate", this);
            }
            public override void DidDeactivate ()
            {
                // This method is called when the watch view controller is no longer visible to the user.
                Console.WriteLine ("{0} did deactivate", this);
            }
        }
    }
    ```

5. 在 " C# **监视应用扩展**" 项目中创建另一个新文件**MyInterfaceController.designer.cs** ，并添加以下代码。 请确保更新命名空间、classname 和 `Register` 属性：

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;

    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```

    > [!TIP]
    > 您可以选择将此文件作为第一个文件的子节点，方法是将其拖到 Visual Studio for Mac C# Solution Pad 的其他文件中。 它将如下所示：

    ![](troubleshooting-images/add-5.png "The Solution pad")

6. 选择 "**生成" > "生成所有**"，以便 Xcode 同步将识别使用的新类（通过 `Register` 属性）。

7. 右键单击 "监视应用情节提要" 文件并选择 "**打开方式" > Xcode "Interface Builder**，重新打开情节提要：

    ![](troubleshooting-images/add-6.png "Opening the storyboard in Interface Builder")

8. 选择新的接口控制器，并为其指定上面定义的类名，例如。 `MyInterfaceController` 列中的一个值匹配。
    如果一切都正常工作，则它应自动显示在**类：** 下拉列表中，你可以从该下拉列表中选择它。

    ![](troubleshooting-images/add-4.png "Setting a custom class")

9. 选择 Xcode 中的 "**助手编辑器**" 视图（具有两个重叠圆圈的图标），以便您可以并排查看情节提要和代码：

    ![](troubleshooting-images/add-7.png "The Assistant Editor toolbar item")

    当焦点位于 "代码" 窗格中时，请确保查看的是 **.h**头文件，如果不在痕迹导航栏中右键单击并选择正确的文件（**MyInterfaceController**）

    ![](troubleshooting-images/add-8.png "Select MyInterfaceController")

10. 你现在可以通过**Ctrl +** 从情节提要拖到 **.h**头文件来创建输出口和操作。

    ![](troubleshooting-images/add-9.png "Creating outlets and actions")

    当你放开拖动时，系统将提示你选择是创建输出口还是使用操作，并选择其名称：

    ![](troubleshooting-images/add-a.png "The outlet and an action dialog")

11. 保存情节提要更改并关闭 Xcode 后，将返回 Visual Studio for Mac。 它将检测头文件更改，并自动将代码添加到**designer.cs**文件中：

    ```csharp
    [Register ("MyInterfaceController")]
    partial class MyInterfaceController
    {
        [Outlet]
        WatchKit.WKInterfaceButton myButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (myButton != null) {
                myButton.Dispose ();
                myButton = null;
            }
        }
    }
    ```

你现在可以在中C#引用控件（或实现操作）！

<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>从命令行启动 Watch 应用

> [!IMPORTANT]
> 默认情况下，你可以在普通应用模式下启动 "监视应用"，也可以使用 Visual Studio for Mac 和 Visual Studio 中的[自定义执行参数](~/ios/watchos/get-started/installation.md#custommodes)**一目了然**或**通知**模式。

你还可以使用命令行来控制 iOS 模拟器。 用于启动监视应用的命令行工具是**mtouch**。

下面是一个完整示例（在终端中作为单行执行）：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

需要更新以反映应用 `launchsimwatch`的参数：

### <a name="--launchsimwatch"></a>--launchsimwatch

适用于 iOS 应用的主应用捆绑包的完整路径 *，其中包含 watch 应用和扩展*。

> [!NOTE]
> 你需要提供的路径适用于*iPhone 应用程序*应用程序文件，即，将部署到 iOS 模拟器并包含监视扩展和监视应用的路径。

示例：

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

## <a name="notification-mode"></a>通知模式

若要测试应用的[**通知**模式](~/ios/watchos/platform/notifications.md)，请将 `watchlaunchmode` 参数设置为 `Notification`，并提供包含测试通知负载的 JSON 文件的路径。

负载参数对于通知模式是*必需*的。

例如，将以下参数添加到 mtouch 命令：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```

## <a name="other-arguments"></a>其他参数

其余参数如下所述：

### <a name="--sdkroot"></a>--sdkroot

必需。 指定 Xcode （6.2 或更高版本）的路径。

示例：

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--设备

要执行的模拟器设备。 这可以通过两种方式来指定：使用特定设备的 udid 或使用运行时和设备类型的组合。

确切的值因计算机而异，可使用 Apple 的**simctl**工具进行查询：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

示例：

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**运行时和设备类型**

示例：

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```

## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
