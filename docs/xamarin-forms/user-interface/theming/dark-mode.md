---
title: 在 Xamarin 中检测暗色模式。窗体应用程序
description: 使用 ResourceDictionaries、DynamicResources 和平台知识的组合，可以在任何 Xamarin 应用程序应用程序中支持暗色模式。
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/19/2020
ms.openlocfilehash: 7136e3240a39321b2d67ca29c16a0758cf5c4cfb
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486288"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>在 Xamarin 中检测暗色模式。窗体应用程序

Xamarin。窗体应用程序可以使用可支持[主题](theming.md)的相同策略来响应操作系统主题更改。 主要区别在于如何触发主题更改。 深色模式指的是一组更广泛的外观首选项，可在操作系统级别进行设置，以及哪些应用程序应遵守并立即响应。

在 Xamarin 中使用暗色模式的最低要求。窗体应用程序包括：

- iOS 13 或更高版本。
- Android 9 或更高版本（Android 9 支持您可以查询的外观模式）。
- Android 10 或更高版本（Android 10 通知应用模式更改）。
- UWP v 10.0.10240.0 或更高版本。
- Xamarin. Forms （任何版本）。

支持外观模式的过程包括浅和暗，如下所示：

1. 在[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定义整个应用程序共享的资源。
2. 为每个外观模式定义一个资源字典主题，该主题提供特定于要更改的每个样式的替代。
3. 在应用程序的**应用程序 .xaml**文件中设置默认外观模式主题。
4. 使用 `DynamicResource` 标记扩展对应用程序进行样式，您希望在外观模式发生变化时，对样式作出反应。
5. 添加用于侦听操作系统主题更改的代码，并加载应用程序的相应主题。

以下屏幕截图显示了浅色和深色模式下的主题页面：

在 ios 和 Android 上的主题应用的详细信息页的[![ios 和 android
屏幕截图上的主题应用的主页屏幕截图](theming-images/main-page-both-themes.png "主题应用的主页")](theming-images/main-page-both-themes-large.png#lightbox "主题应用的主页") [ ![](theming-images/detail-page-both-themes.png "主题应用的详细信息页")](theming-images/detail-page-both-themes-large.png#lightbox "主题应用的详细信息页")

## <a name="define-themes"></a>定义主题

有关创建深色和浅色主题的分步详细信息，请参阅[主题指南](theming.md)。

## <a name="react-to-appearance-mode-changes"></a>反应外观模式更改

设备上的外观模式可能因多种原因而发生变化，这取决于用户配置其首选项的方式，包括明确选择模式、时间或环境因素（如低轻型）。 你需要添加平台代码以确保你的应用程序可以对这些更改做出反应，以下部分更详细地讨论了这一点。

### <a name="android"></a>Android

在应用程序的**MainActivity.cs**文件中，将 `ConfigChanges.UiMode` 字段添加到 `Activity` 特性中的 `ConfigurationChanges` 属性中，以便应用程序的 UI 模式发生更改时收到通知：

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

在同一**MainActivity.cs**文件中，重写 `OnConfigurationChanged` 方法：

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);

    if ((newConfig.UiMode & UiMode.NightNo) != 0)
    {
        // change to light theme
        // e.g. App.Current.Resources = new YourLightTheme();
    }
    else
    {
        // change to dark theme
        // e.g. App.Current.Resources = new YourDarkTheme();
    }
}
```

### <a name="ios"></a>iOS

在 iOS 上，外观模式更改会在 UIViewController 上收到通知，因为 Forms 是 `ContentPage`。 若要重写该方法，请在 iOS 项目中创建一个名为的自定义呈现器 `PageRenderer.cs`：

```csharp
using System;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(ContentPage), typeof(YourApp.iOS.Renderers.PageRenderer))]
namespace YourApp.iOS.Renderers
{
    public class PageRenderer : Xamarin.Forms.Platform.iOS.PageRenderer
    {
        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetAppTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            if(this.TraitCollection.UserInterfaceStyle != previousTraitCollection.UserInterfaceStyle)
            {
                SetAppTheme();
            }
        }

        void SetAppTheme()
        {
            if (this.TraitCollection.UserInterfaceStyle == UIUserInterfaceStyle.Dark)
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            }
            else
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            }
        }
    }
}
```

重写 `TraitCollectionDidChange` 方法使您可以对 `UserInterfaceStyle` 的更改进行操作。

### <a name="uwp"></a>UWP

在 UWP 上，将以下代码添加到应用程序的**MainPage.xaml.cs**文件中：

```csharp
public sealed partial class MainPage
{

    UISettings uiSettings;

    public MainPage()
    {
        this.InitializeComponent();

        LoadApplication(new YourApp.App());

        uiSettings = new UISettings();
        uiSettings.ColorValuesChanged += ColorValuesChanged;
    }

    private void ColorValuesChanged(UISettings sender, object args)
    {
        var backgroundColor = sender.GetColorValue(UIColorType.Background);
        var isDarkMode = backgroundColor == Colors.Black;
        if(isDarkMode)
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            });
        }
        else
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            });
        }
    }
}
```

## <a name="set-dark-and-light-themes"></a>设置深色和浅色主题

遵循[主题](theming.md)指南后，可以在上述平台代码的适当位置轻松为应用程序设置新的主题。 若要加载新主题，只需将应用程序的当前资源字典替换为所选的主题资源字典：

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="related-links"></a>相关链接

- [主题（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin 中的动态样式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)
