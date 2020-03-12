---
title: 第 4 部分 - 处理多个平台
description: 本文档介绍如何根据平台或功能处理应用程序的分歧。 它讨论了屏幕大小、导航的形式、触摸和手势、推送通知和接口模式，如列表和选项卡。
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: c8b4dcbfbf65bc4059125404b0d20ed35fa31f29
ms.sourcegitcommit: ce4670de51e24116a944c778ee64585bd0aae0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088928"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>第 4 部分 - 处理多个平台

## <a name="handling-platform-divergence-amp-features"></a>处理平台分歧 &amp; 功能

分歧并不只是一个 "跨平台" 问题;"相同" 平台上的设备具有不同的功能（特别是可用的各种 Android 设备）。 最明显的基本功能是屏幕大小，但其他设备属性可能会有所不同，并且需要应用程序检查某些功能，并根据其存在（或缺少）的行为。

这意味着所有应用程序都需要处理正常的功能降级，否则提供不严重的最小标准-分母功能集。 Xamarin 与每个平台的本机 Sdk 的深度集成允许应用程序利用特定于平台的功能，因此设计应用程序以使用这些功能是有意义的。

有关平台功能的不同概述，请参阅平台功能文档。

## <a name="examples-of-platform-divergence"></a>平台分歧示例

### <a name="fundamental-elements-that-exist-across-platforms"></a>跨平台存在的基本元素

移动应用程序有一些通用特性。
这些都是所有设备通常都适用的高级概念，因此可以构成应用程序设计的基础：

- 通过选项卡或菜单的功能选择
- 数据和滚动列表
- 数据的单一视图
- 编辑数据的单一视图
- 向后导航

设计高级屏幕流时，可以基于这些概念的常见用户体验。

### <a name="platform-specific-attributes"></a>平台特定的属性

除了所有平台上存在的基本元素外，还需要解决设计中的关键平台差异。 你可能需要考虑（并专门编写代码来处理）这些差异：

- **屏幕大小**–某些平台（例如 iOS 和更早的 Windows Phone 版本）具有相对较简单的标准屏幕大小。 Android 设备的屏幕尺寸很大，需要更多精力才能在应用程序中提供支持。
- **导航**形式–不同于多种平台（例如 硬件 "后退" 按钮、全景 UI 控件）和平台内部（Android 2 和4、iPhone 和 iPad）。
- **键盘**–某些 Android 设备具有物理键盘，而有些则仅具有软件键盘。 检测软键盘隐藏部分屏幕的代码需要对这些差异敏感。
- **触摸和手势**–对笔势识别的操作系统支持会有所不同，尤其是在每个操作系统的较旧版本中。 较早版本的 Android 对触控操作的支持非常有限，这意味着支持较旧的设备可能需要单独的代码
- **推送通知**–每个平台都有不同的功能/实现方式（例如 Windows 上的动态磁贴）。

### <a name="device-specific-features"></a>设备特定的功能

确定应用程序所需的最小功能必须为;或者决定要在每个平台上利用的其他功能。 需要代码来检测功能并禁用功能或提供替代项（例如 地理位置的替代方法是允许用户键入位置或从映射中进行选择）：

- **相机**–不同设备上的功能不同：某些设备没有相机，其他设备有正面和背面相机。 有些相机能够录像。
- **地理位置 & maps** –所有设备上都不支持 GPS 或 wi-fi 位置。 应用程序还需要满足每个方法所支持的不同准确性级别。
- **加速感应程序、陀螺仪和罗盘**–这些功能通常仅在每个平台上的一种设备中找到，因此在硬件不受支持时，应用程序几乎始终需要提供回退。
- **Twitter 和 Facebook** –分别仅限 IOS5 和 iOS6 上的 "内置"。 在早期版本和其他平台上，你将需要直接向每个服务的 API 提供自己的身份验证函数和接口。
- **近现场通信（NFC）** -仅限（某些） Android 手机上的（在撰写时）。

## <a name="dealing-with-platform-divergence"></a>处理平台分歧

可以通过两种不同的方法从相同的代码库支持多个平台，每个平台都有自己的优点和缺点。

- **平台抽象**–业务外观模式，提供跨平台的统一访问，并将特定平台实现抽象化为单一的统一 API。
- **分歧实现**–通过结构工具（如接口和继承）或条件编译等各种实现，调用特定平台功能。

## <a name="platform-abstraction"></a>平台抽象

### <a name="class-abstraction"></a>类抽象

使用共享代码中定义的接口或基类，并在平台特定的项目中实现或扩展。 使用类抽象编写和扩展共享代码尤其适用于可移植类库，因为它们具有可供其使用的有限的框架子集，并且不能包含编译器指令以支持特定于平台的代码分支。

#### <a name="interfaces"></a>界面

使用接口可以实现特定于平台的类，这些类仍可传递到共享库以利用通用代码。

接口在共享代码中定义，并作为参数或属性传递到共享库。

然后，特定于平台的应用程序可以实现接口，但仍可利用共享代码对其进行 "处理"。

 **优点**

实现可以包含特定于平台的代码，甚至还可以引用特定于平台的外部库。

 **缺点**

必须创建实现并将其传递到共享代码中。 如果在共享代码中使用了深层接口，则它最终将通过多个方法参数传递，或通过调用链向下推送。 如果共享代码使用许多不同的接口，则必须在共享代码中创建并设置它们。

#### <a name="inheritance"></a>继承

共享代码可以实现抽象类或虚拟类，这些类可在一个或多个特定于平台的项目中扩展。 这类似于使用接口，但已实现某些行为。 接口或继承是否是更好的设计选择会有不同的观点：尤其是C#因为仅允许单一继承，所以它可以决定 api 的设计方式。 使用继承时要谨慎。

接口的优点和缺点同样适用于继承，另外还具有基类可以包含某些实现代码的优点（可能是可以选择性扩展的整个平台无关实现）。

## <a name="xamarinforms"></a>Xamarin.Forms

请参阅[Xamarin](~/get-started/index.yml)文档。

### <a name="other-cross-platform-libraries"></a>其他跨平台库

这些库还为C#开发人员提供了跨平台功能：

- [**Xamarin**](~/essentials/index.md) –用于常见功能的跨平台 api。
- [**SkiaSharp**](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) –跨平台2d 图形。

## <a name="conditional-compilation"></a>条件编译

在某些情况下，您的共享代码仍需要在每个平台上以不同方式工作，这可能会访问行为不同的类或功能。 条件编译最适用于共享资产项目，其中在定义了不同符号的多个项目中引用了同一个源文件。

Xamarin 项目始终定义 `__MOBILE__` 这对于 iOS 和 Android 应用程序项目均为 true （请注意，这两个符号上的双下划线预修复和后修复方法相同）。

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

#### <a name="ios"></a>iOS

Xamarin 定义可用于检测 iOS 设备 `__IOS__`。

```csharp
#if __IOS__
// iOS-specific code
#endif
```

还提供了特定于手表和电视的符号：

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

只应编译为 Xamarin Android 应用程序的代码可以使用以下

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

每个 API 版本还定义了一个新的编译器指令，因此，如果针对的是较新的 Api，则此类代码将允许你添加功能。 每个 API 级别都包含 "较低" 级别的符号。 此功能对于支持多个平台并不真正有用;通常 `__ANDROID__` 符号就足够了。

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Xamarin 会定义 `__MACOS__`，仅可用于对 macOS 进行编译：

```csharp
#if __MACOS__
// macOS-specific code
#endif
```

#### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

请使用 `WINDOWS_UWP`。 字符串周围没有下划线，如 Xamarin 平台符号。

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>使用条件编译

条件编译的一个简单的案例研究示例是设置 SQLite 数据库文件的文件位置。 对于指定文件位置，这三个平台有略微不同的要求：

- **iOS** – Apple 首选非用户数据放置在特定位置（库目录），但此目录没有系统常量。 需要特定于平台的代码来生成正确的路径。
- **Android** – `Environment.SpecialFolder.Personal` 返回的系统路径是存储数据库文件的可接受位置。
- **Windows Phone** –独立存储机制不允许指定完整路径，只允许指定相对路径和文件名。
- **通用 Windows 平台**–使用 `Windows.Storage` api。

下面的代码使用条件编译来确保每个平台的 `DatabaseFilePath` 都是正确的：

```csharp
public static string DatabaseFilePath
{
    get
    {
        var filename = "TodoDatabase.db3";
#if SILVERLIGHT
        // Windows Phone 8
        var path = filename;
#else

#if __ANDROID__
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine(libraryPath, filename);
#endif
        return path;
    }
}
```

结果就是可以在所有平台上生成和使用的类，在每个平台上将 SQLite 数据库文件放在不同的位置。
