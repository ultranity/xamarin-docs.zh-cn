---
title: Xamarin.Forms App 类
description: 本文说明默认 App 类的功能，其中包括一个可设置应用初始页面的属性，以及一个跨生命周期状态更改存储简单值的持久字典。
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
ms.custom: video
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: aaf2086fd8128d68baa401ab646b31bcbc279545
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916488"
---
# <a name="xamarinforms-app-class"></a>Xamarin.Forms App 类

`Application` 基类提供以下功能，这些功能在项目的默认 `App` 子类中公开：

* `MainPage` 属性，可在其中设置应用的初始页面。
* 持久 [`Properties` 字典](#Properties_Dictionary)，用于跨生命周期状态更改存储简单值。
* 静态 `Current` 属性，其中包含对当前应用程序对象的引用。

它还公开[生命周期方法](~/xamarin-forms/app-fundamentals/app-lifecycle.md)，例如 `OnStart`、`OnSleep` 和 `OnResume` 以及模式导航事件。

根据选择的模板，可以通过以下两种方式之一定义 `App` 类：

* **C#** ，或
* **XAML 和 C#**

若要使用 XAML 创建 **App** 类，必须使用 XAML **App** 类和关联的代码隐藏替换默认的 **App** 类，如以下代码示例所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Photos.App">

</Application>
```

以下代码示例展示了关联的代码隐藏：

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

除了设置 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 属性外，代码隐藏还必须调用 `InitializeComponent` 方法来加载和分析相关的 XAML。

## <a name="mainpage-property"></a>MainPage 属性

`MainPage` 类上的 `Application` 属性可设置应用程序的根页面。

例如，可在 `App` 类中创建逻辑，以便根据用户的登录状态来显示不同的页面。

应在 `MainPage` 构造函数中设置 `App` 属性。

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>属性字典

`Application` 子类具有静态 `Properties` 字典，该字典可用于存储数据，特别是用于 `OnStart`、`OnSleep` 和 `OnResume` 方法。 可以使用 `Application.Current.Properties` 从 Xamarin.Forms 代码中的任何位置对此进行访问。

`Properties` 字典使用 `string` 密钥并存储 `object` 值。

例如，可以在代码中的任何位置（选中某个项目时，在页面的 `"id"` 方法中，或在 `OnDisappearing` 方法中）设置持久 `OnSleep` 属性，如下所示：

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

在 `OnStart` 或 `OnResume` 方法中，随后可使用此值以某种方式重新创建用户体验。 `Properties` 字典存储 `object`，因此，需要在使用之前强制转换其值。

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

在访问密钥前，请务必检查密钥是否存在，以防止意外错误。

> [!NOTE]
> `Properties` 字典只能序列化基元类型用于存储。 尝试存储其他类型（例如 `List<string>`）可能会在无提示的情况下失败。

<!-- bugzilla 28657 -->

### <a name="persistence"></a>持久性

`Properties` 字典会自动保存到设备中。
当应用程序从后台返回，甚或在其重启后，添加到字典中的数据将可用。

Xamarin.Forms 1.4 在 `Application` 类上引入了其他方法 (`SavePropertiesAsync()`)，可调用该方法来主动保存 `Properties` 字典。 这是为了让用户能够在重要更新后保存属性，而不用冒因崩溃或被 OS 终止而无法将这些属性序列化的风险。

可在`Properties`使用 Xamarin.Forms 创建移动应用**一书的第** 6[、](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf)15[ 和 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)20[ 章以及相关](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)示例[中找到如何使用 ](https://github.com/xamarin/xamarin-forms-book-preview-2) 字典的参考。

## <a name="the-application-class"></a>应用程序类

完整的 `Application` 类实现如下所示，以供参考：

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}
```

此类随后在每个特定于平台的项目中实例化并传递到 `LoadApplication` 方法，该方法可供加载 `MainPage` 并向用户显示。
以下各部分展示了每个平台的代码。 最新的 Xamarin.Forms 解决方案模板已包含所有这些为应用预先配置的代码。

### <a name="ios-project"></a>iOS 项目

iOS `AppDelegate` 类继承自 `FormsApplicationDelegate`。 它应该：

* 使用 `LoadApplication` 类的实例调用 `App`。

* 始终返回 `base.FinishedLaunching (app, options);`。

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Android 项目

Android `MainActivity` 继承自 `FormsAppCompatActivity`。 在 `OnCreate` 替代中，使用 `LoadApplication` 类的实例调用 `App` 方法。

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>适用于 Windows 10 的通用 Windows 项目 (UWP)

UWP 项目中的主页应继承自 `WindowsPage`：

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

C# 代码隐藏构造必须调用 `LoadApplication` 来创建 Xamarin.Forms `App` 的实例。 请注意，最好显式使用应用程序命名空间来限定 `App`，因为 UWP 应用程序还有自己的与 Xamarin.Forms 无关的 `App` 类。

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

请注意，必须从 UWP 项目中的 App.xaml.cs `Forms.Init()`**调用** 。

有关详细信息，请参阅[安装 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)，其中包括将 UWP 项目添加到现有的不面向 UWP 的 Xamarin.Forms 解决方案的步骤。

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
