---
title: Xamarin.Essentials:权限
description: 本文档介绍了 Xamarin.Essentials 中的 Permissions 类，它提供了检查和请求运行时权限的功能。
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 21f2079ace4adae6fd84d89426e5d66692af2a0a
ms.sourcegitcommit: ddf0369573b738896efaa601fa6ef5dbc68d9cf1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78289798"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials:权限

Permissions  类提供了检查和请求运行时权限的功能。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>使用 Permissions

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>检查权限

若要检查权限的当前状态，请使用 `CheckStatusAsync` 方法以及要获取其状态的特定权限。

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

如果相应权限未声明，`PermissionException` 则会抛出。

## <a name="requesting-permissions"></a>请求权限

若要向用户请求权限，请使用 `RequestAsync` 方法以及要请求的特定权限。 如果用户先前授予了权限，并且没有撤销它，此方法会立即返回 `Granted`，而不会显示对话框。 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

如果相应权限未声明，`PermissionException` 则会抛出。 

请注意，在某些平台上，权限请求只能激活一次。 开发人员必须处理其他提示，以检查权限是否处于 `Denied` 状态，并要求用户手动启用它。

## <a name="permission-status"></a>权限状态

如果使用的是 `CheckStatusAsync` 或 `RequestAsync`，将返回用于确定后续步骤的 `PermissionStatus`。

* Unknown - 权限处于未知状态
* Denied - 用户拒绝了权限请求
* Disabled - 此功能在设备上是禁用的
* Granted - 用户已授予权限或权限已自动授予
* Restricted - 处于受限制状态

## <a name="available-permissions"></a>可用权限

Xamarin.Essentials 尝试提取尽可能多的权限，但每个操作系统都有一组不同的运行时权限。 此外，在能否为某些权限提供单个 API 方面也存在差异。 下面是当前可用权限的指南：

图标指南：

* ![完全支持](~/media/shared/yes.png "完全支持") - 支持
* ![不支持](~/media/shared/no.png "不支持或不需要") - 不支持/不需要

| 权限 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| CalendarWrite | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 照相机 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| ContactsRead | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| ContactsWrite | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 手电筒 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![iOS 不支持](~/media/shared/no.png "iOS 不支持") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| LocationWhenInUse | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS")  | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| LocationAlways | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| 媒体 | ![Android 不支持](~/media/shared/no.png "Android 不支持") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 麦克风 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| 电话 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 照片 | ![Android 不支持](~/media/shared/no.png "Android 不支持") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 提醒 | ![Android 不支持](~/media/shared/no.png "Android 不支持") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| Sensors | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| Sms | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 语音 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| StorageRead | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![iOS 不支持](~/media/shared/no.png "iOS 不支持") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| StorageWrite | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![iOS 不支持](~/media/shared/no.png "iOS 不支持") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |

如果权限标有 ![not supported](~/media/shared/no.png "不受支持")，则在检查或请求权限时始终返回 `Granted`。

## <a name="general-usage"></a>常规使用情况
下面介绍了权限的一般处理模式。

```csharp
public async Task<PermissionStatus> CheckAndRequestPermissionAsync<TPermission>()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

每个权限类型都能创建实例，可以对其直接调用方法。

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>扩展 Permissions

Permissions API 灵活且可扩展，可用于需要额外验证或权限的应用，而这些验证或权限未包含在 Xamarin.Essentials 中。 新建继承自 `BasePermission` 的类，并实现相应抽象方法。 Then 

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

在特定平台中实现权限时，可以继承自 `BasePlatformPermission` 类。 这提供了额外的平台帮助程序方法，用于自动检查声明。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="android"></a>[Android](#tab/android)

Permissions 必须在 Android 清单文件中设置匹配的属性。

有关详细信息，请参阅 [Xamarin.Android 中的 Permissions](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions) 文档。

# <a name="ios"></a>[iOS](#tab/ios)

Permissions 必须在 `Info.plist` 文件中有匹配字符串。 在权限获得请求并遭拒后，如果第二次请求权限，将不再显示弹出窗口。 必须提示用户在 iOS 的“应用设置”屏幕中手动调整设置。

有关详细信息，请参阅 [iOS 安全和隐私功能](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy)文档。

# <a name="uwp"></a>[UWP](#tab/uwp)

Permissions 必须有在包清单中声明的匹配功能。

有关详细信息，请参阅[应用功能声明](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)文档。

--------------

## <a name="api"></a>API

- [Permissions 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Permissions API 文档](xref:Xamarin.Essentials.Permissions)

