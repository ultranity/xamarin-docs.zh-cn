---
title: Unified API 概述
description: Xamarin 的 Unified API 允许在 Mac 和 iOS 之间共享代码，并支持具有相同二进制文件的32和64位应用程序。
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 8402a48602dd94578e688faeb038aec69684e7d4
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917559"
---
# <a name="unified-api-overview"></a>Unified API 概述

Xamarin 的 Unified API 允许在 Mac 和 iOS 之间共享代码，并支持具有相同二进制文件的32和64位应用程序。 默认情况下，在新的 Xamarin 和 Xamarin 项目中使用 Unified API。

> [!IMPORTANT]
> 已不推荐使用 Unified API 之前的 Xamarin Classic API。
>
> - 支持 Classic API （monotouch.dialog）的最新版本的 Xamarin 是 Xamarin 9.10。
> - Xamarin 仍支持 Classic API，但不再更新。 由于已弃用，因此开发人员应将其应用程序移到 Unified API。

## <a name="updating-classic-api-based-apps"></a>更新基于 Classic API 的应用

遵循适用于你的平台的相关说明：

- [更新现有应用](updating-apps.md)
- [更新现有 iOS 应用](updating-ios-apps.md)
- [更新现有 Mac 应用](updating-mac-apps.md)
- [更新现有 Xamarin.Forms 应用](updating-xamarin-forms-apps.md)
- [将绑定迁移到 Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-api"></a>[将代码更新为 Unified API 的提示](updating-tips.md)

不管你要迁移哪些应用程序，请查看[以下提示](updating-tips.md)，以帮助你成功更新到 Unified API。

## <a name="library-split"></a>库拆分

从此时开始，我们的 Api 将以两种方式出现：

- **Classic API：** 仅限32位（仅限），在 `monotouch.dll` 和 `XamMac.dll` 程序集中公开。
- **Unified API：** 使用 `Xamarin.iOS.dll` 和 `Xamarin.Mac.dll` 程序集中提供的单个 API 支持32和64位开发。

这意味着，对于企业开发人员（而不是以应用商店为目标），可以继续使用现有的经典 Api，因为我们将永久保留它们，或者可以升级到新的 Api。

<a name="namespace-changes" />

## <a name="namespace-changes"></a>命名空间更改

为了减少在 Mac 和 iOS 产品之间共享代码的摩擦，我们正在更改产品中的 Api 的命名空间。

我们将从我们的 iOS 产品中删除前缀 "Monotouch.dialog"，并从我们的 Mac 产品中删除数据类型的 "MonoMac"。

这样就可以更轻松地在 Mac 和 iOS 平台之间共享代码，而无需进行条件编译，并会减少源代码文件顶部的干扰。

- **Classic API：** 命名空间使用 `MonoTouch.` 或 `MonoMac.` 前缀。
- **Unified API：** 没有命名空间前缀

## <a name="runtime-defaults"></a>运行时默认值

默认情况下，Unified API 使用**SGen**垃圾回收器和[新的引用计数](~/ios/internals/newrefcount.md)系统来跟踪对象所有权。 此同一功能已移植到 Xamarin。

这解决了开发人员在使用旧系统时遇到的许多问题，同时简化了[内存管理](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

请注意，即使是 Classic API 也可以启用 New 引用计数，但默认值为保守，无需用户进行任何更改。 在 Unified API 中，我们采取了更改默认值的机会，并为开发人员提供了在其重构和重新测试代码的同时进行的所有改进。

## <a name="api-changes"></a>API 更改

Unified API 将删除已弃用的方法，并且在 API 名称中有几个错误绑定到经典 Api 中的原始 Monotouch.dialog 和 MonoMac 命名空间。 这些实例在新的统一 Api 中得到了更正，需要在组件、iOS 和 Mac 应用程序中进行更新。 下面列出了你可能会遇到的最常见的情况：

|Classic API 方法名称|Unified API 方法名称|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

有关从经典部署模型切换到 Unified API 时所发生的更改的完整列表，请参阅我们的[经典（monotouch.dialog） Vs 统一（Xamarin） API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)文档。

## <a name="updating-to-unified"></a>正在更新为统一

**传统**上的多个旧/损坏/弃用的 Api 在**统一**API 中不可用。 在启动（手动或自动）升级之前，可以更轻松地修复 `CS0616` 警告，因为你将获得 `[Obsolete]` 的属性消息（部分警告），以指导你使用正确的 API。

请注意，我们将发布可在项目更新之前或之后使用的经典与统一 API 更改的[*不同*](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)之处。 仍修复经典中的 obsoletes 调用通常是一种时间保护（更少的文档查找）。

按照这些说明操作，将[现有 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)或[Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)更新到 Unified API。
查看本页的其余部分，并查看有关迁移代码的其他信息的[提示](~/cross-platform/macios/unified/updating-tips.md)。

### <a name="nuget"></a>NuGet

以前支持 Xamarin 的 NuGet 包 Classic API 通过使用**Monotouch10**平台名字对象发布其程序集。

Unified API 引入了兼容包的新平台标识符- **iOS10**。 需要更新现有 NuGet 包以添加对此平台的支持，方法是针对 Unified API 进行构建。

> [!IMPORTANT]
> 如果在同一 Xamarin 中出现错误，格式为 _"错误3不能同时包括 ' monotouch.dialog ' 和 ' Xamarin. .dll '"。 iOS 项目-"Xamarin. .dll" 是显式引用的，而 "monotouch.dialog" 是在将应用程序转换为统一 api 后由 "xxx，Version = 0.0.000，Culture = 中立，PublicKeyToken = null" "引用_的，则通常是因为项目中的组件或 NuGet 包尚未更新到 Unified API。 需要删除现有的组件/NuGet，更新到支持统一 Api 的版本，并执行干净的生成。

### <a name="the-road-to-64-bits"></a>64位的道路

有关支持32和64位应用程序的背景和有关框架的信息，请参阅[32 和64位平台注意事项](~/cross-platform/macios/32-and-64/index.md)。

 <a name="new-data-types" />

#### <a name="new-data-types"></a>新数据类型

不同之处在于，Mac 和 iOS Api 使用一种特定于体系结构的数据类型，这些数据类型在32位平台上始终32位，在64位平台上使用64位。

例如，客观-C 将 `NSInteger` 数据类型映射到32位系统上的 `int32_t`，并映射到64位系统上的 `int64_t`。

若要匹配此行为，在我们的 Unified API 中，我们将替换以前的 `int` （在 .NET 中，将其定义为始终 `System.Int32`）到新的数据类型： `System.nint`。  可以将 "n" 视为 "本机"，因此可以将平台的本机整数类型视为。

我们正在介绍 `nint`、`nuint` 和 `nfloat`，同时提供在必要时在其基础上构建的数据类型。

若要了解有关这些数据类型更改的详细信息，请参阅[本机类型](~/cross-platform/macios/nativetypes.md)文档。

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>如何检测 iOS 应用程序的体系结构

在某些情况下，应用程序需要知道它是在32位还是64位 iOS 系统上运行。 下面的代码可用于检查体系结构：

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>数组和 System.object

由于C#索引器需要某种类型的 `int`，因此必须将 `nint` 值显式转换为 `int`，以便访问集合或数组中的元素。 例如：

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

这是预期的行为，因为从 `int` 到 `nint` 的强制转换在64位上丢失，因此不会执行隐式转换。

### <a name="converting-datetime-to-nsdate"></a>将 DateTime 转换为 NSDate

使用统一 Api 时，将不再执行 `DateTime` 到 `NSDate` 值的隐式转换。 这些值需要从一种类型显式转换为另一种类型。 以下扩展方法可用于自动执行此过程：

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>弃用的 Api 和拼写错误

在 Xamarin iOS 经典 API （monotouch.dialog）中，使用了两种不同方式的 `[Obsolete]` 属性：

- **弃用的 IOS API：** 这是因为 Apple 提示您停止使用 API，因为它被更新了。 Classic API 仍可正常工作，并且通常是必需的（如果支持旧版本的 iOS）。
 此类 API （和 `[Obsolete]` 属性）包含在新的 Xamarin iOS 程序集中。
- **API 不正确**某些 API 的名称上有错误。

对于原始程序集（monotouch.dialog 和 XamMac），我们保存了旧代码以实现兼容，但已将其从 Unified API 程序集（Xamarin. .dll 和 Xamarin）中删除。

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject （IntPtr）

每个 `NSObject` 子类都包含一个接受 `IntPtr`的构造函数。 这就是我们如何通过本机 ObjC 句柄来实例化新的托管实例。

在经典中，这是一个 `public` 构造函数。 但是，在用户代码中使用此功能很容易，例如，为单个 ObjC 实例创建多个托管实例，*或者*创建将缺少预期托管状态（对于子类）的托管实例。

为避免出现这种问题，`IntPtr` 构造函数现在 `protected` 在**统一**API 中，仅用于子类化。 这将确保使用正确的/安全 API 通过句柄创建托管实例，即

```csharp
var label = Runtime.GetNSObject<UILabel> (handle);
```

此 API 将返回现有的托管实例（如果已存在）或将创建一个新实例（如果需要）。 它已在经典 API 和统一 API 中可用。

请注意，现在还 `protected` `.ctor(NSObjectFlag)`，但这种情况很少在子类外使用。

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction 已替换为操作

对于统一 Api，`NSAction` 已被删除以支持标准 .NET `Action`。 这是一个很大的改进，因为 `Action` 是一种常见的 .NET 类型，而 `NSAction` 特定于 Xamarin。 它们都执行完全相同的操作，但它们是不同且不兼容的类型，导致编写更多的代码来实现相同的结果。

例如，如果你的现有 Xamarin 应用程序包含以下代码：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```

现在可将其替换为简单的 lambda：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

以前，由于无法将 `Action` 分配给 `NSAction`，因此会导致编译器错误，但由于 `UITapGestureRecognizer` 现在使用 `Action` 而不是 `NSAction` 它在统一 Api 中是有效的。

### <a name="custom-delegates-replaced-with-actiont"></a>自定义委托替换为操作\<T >

在**统一**的部分（例如一个参数）中，.net 委托已替换为 `Action<T>`。 例如

```csharp
public delegate void NSNotificationHandler (NSNotification notification);
```

现在可用作 `Action<NSNotification>`。 这会在 Xamarin 和你自己的应用程序中提高代码重用性并减少代码重复。

### <a name="taskbool-replaced-with-taskbooleannserror"></a>任务\<bool > 替换为 Task < Boolean，NSError > >

在**经典**中，有一些异步 api 返回 `Task<bool>`。 但是，其中某些在 `NSError` 是签名的一部分时使用，即 `bool` 已 `true`，并且你必须捕获异常以获取 `NSError`。

由于一些错误很常见，并且返回值无效，因此此模式在**统一**中更改为返回 `Task<Tuple<Boolean,NSError>>`。 这使你可以检查成功和异步调用期间可能发生的任何错误。

### <a name="nsstring-vs-string"></a>NSString vs string

在少数情况下，必须将一些常量从 `string` 更改为 `NSString`，例如 `UITableViewCell`

**经典**

```csharp
public virtual string ReuseIdentifier { get; }
```

**统**

```csharp
public virtual NSString ReuseIdentifier { get; }
```

通常，我们更喜欢 .NET `System.String` 类型。 但尽管有 Apple 准则，但某些本机 API 却比较了常量指针（而不是字符串本身），这仅在作为 `NSString`公开常量时才起作用。

 <a name="protocols" />

### <a name="objective-c-protocols"></a>目标-C 协议

原始 Monotouch.dialog 未对 ObjC 协议提供完全支持，并且添加了一些非最佳的 API，以支持最常见的方案。 此限制不存在，但为了向后兼容，多个 Api 将保留在 `monotouch.dll` 和 `XamMac.dll`中。

这些限制已在统一 Api 上删除并清除。 大多数更改将如下所示：

**经典**

```csharp
public virtual AVAssetResourceLoaderDelegate Delegate { get; }
```

**统**

```csharp
public virtual IAVAssetResourceLoaderDelegate Delegate { get; }
```

`I` 前缀意味着**统一**公开接口，而不是特定类型的 ObjC 协议。 这样就可以轻松地不希望为 Xamarin 提供的特定类型划分子类。

它还允许更精确、更易于使用的 API，例如：

**经典**

```csharp
public virtual void SelectionDidChange (NSObject uiTextInput);
```

**统**

```csharp
public virtual void SelectionDidChange (IUITextInput uiTextInput);
```

此类 API 现在更易于我们，无需参考文档，IDE 代码完成将为你提供基于协议/接口的更多有用建议。

#### <a name="nscoding-protocol"></a>NSCoding 协议

对于每个类型，我们的原始绑定都包含 .ctor （NSCoder），即使它不支持 `NSCoding` 协议。  `NSObject` 中存在单个 `Encode(NSCoder)` 方法，用于对对象进行编码。
但仅当实例与 NSCoding 协议相符时，此方法才起作用。

在 Unified API 修复了此项。  如果类型符合 `NSCoding`，则新的程序集将只有 `.ctor(NSCoder)`。 此外，此类类型现在具有一个符合 `INSCoding` 接口的 `Encode(NSCoder)` 方法。

低影响：在大多数情况下，此更改不会影响应用程序，因为旧的、删除的构造函数无法使用。

## <a name="further-tips"></a>更多提示

要注意的其他更改会在将[应用程序更新到 Unified API 的提示](~/cross-platform/macios/unified/updating-tips.md)中列出。


## <a name="related-links"></a>相关链接

- [更新 iOS 应用程序](updating-ios-apps.md)
- [更新 Mac 应用](updating-mac-apps.md)
- [更新 Xamarin 应用程序](updating-xamarin-forms-apps.md)
- [更新绑定](update-binding.md)
- [更新提示](updating-tips.md)
- [经典与 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
