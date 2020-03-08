---
title: Xamarin 中的 Web 视图
description: 本文档介绍了 Xamarin iOS 应用可显示 web 内容的各种方式。 它讨论了 WKWebView、SFSafariViewController、Safari 和应用传输安全性。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 8640800717a88e800503e93c339eeb080707374e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915699"
---
# <a name="web-views-in-xamarinios"></a>Xamarin 中的 Web 视图

在 iOS 的生存期内，应用开发人员可以通过多种方式在其应用中合并 web 视图功能。 大多数用户都利用其 iOS 设备上的内置 Safari web 浏览器，因此，从其他应用程序获得的 web 视图功能与此体验一致。 它们期望使用相同的笔势，性能应为 par，并具有相同的功能。

iOS 11 引入了对 `WKWebView` 和 `SFSafariViewController`的新更改。 有关这些内容的详细信息，请参阅[iOS 11 指南中的 Web 更改](~/ios/platform/introduction-to-ios11/web.md)。

## <a name="wkwebview"></a>WKWebView

iOS 8 中引入了 `WKWebView`，应用程序开发人员可以实现类似于 mobile Safari 的 web 浏览界面。 这在某种程度上是因为 `WKWebView` 使用 Nitro Javascript 引擎，这是移动 Safari 使用的同一个引擎。 应始终在 UIWebView 上使用 `WKWebView`，因为这样可能会提高性能、内置用户友好的手势，以及网页与应用程序之间的轻松交互。

可以通过与 UIWebView 几乎完全相同的方式将 `WKWebView` 添加到应用程序中，但作为开发人员，你可以更好地控制 UI/UX 和功能。 创建和显示 web 视图对象将显示请求的页面，但你可以控制视图的显示方式、用户可以导航的方式，以及用户如何退出视图。  

下面的代码可用于在 Xamarin iOS 应用程序中启动 `WKWebView`：

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

需要注意的是，`WKWebView` 在 `WebKit` 命名空间中，因此必须将此 using 指令添加到类的顶部。

`WKWebView` 也可用于 Xamarin 应用程序中，如果要创建跨平台 Mac/iOS 应用，则应使用该应用。

[处理 Javascript 警报](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)食谱还提供有关将 WKWebView 与 JavaScript 一起使用的信息。

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` 是提供应用程序提供的 web 内容的最新方式，可在 iOS 9 及更高版本中使用。 与 `UIWebView` 或 `WKWebView`不同，`SFSafariViewController` 是一个视图控制器，因此不能与其他视图一起使用。 你应以新的视图控制器形式显示 `SFSafariViewController`，其方式与显示任何视图控制器的方式相同。

 `SFSafariViewController` 本质上是可以嵌入到应用中的 "小型 safari"。 与 WKWebView 一样，它使用相同的 Nitro Javascript 引擎，还提供了一系列附加的 Safari 功能，如自动填充、读者，以及在移动 Safari 中共享 cookie 和数据的功能。 您的应用程序不能访问用户与 `SFSafariViewController` 之间的交互。 您的应用程序将不能访问任何默认的 Safari 功能。

默认情况下，它还实现 "**完成**" 按钮，使用户能够轻松返回到你的应用程序、前进和后退导航按钮，使用户能够在网页堆栈中导航。 此外，它还为用户提供了一个地址栏，使他们可以放心地使用它们。 地址栏不允许用户更改 url。

这些实现无法更改，因此，如果您的应用程序想要在不进行任何自定义的情况下显示网页，则最好将其用作默认浏览器 `SFSafariViewController`。

下面的代码可用于在 Xamarin iOS 应用程序中启动 `SFSafariViewController`：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

这会生成以下 web 视图：

[![使用 SFSafariViewController 的示例 web 视图](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

还可以通过使用以下代码，从应用内打开移动 Safari 应用：

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

这会生成以下 web 视图：

[![Safari 中显示的网页](webview-images/safari.png)](webview-images/safari.png#lightbox)

通常应始终避免将用户从应用导航到 Safari。 大多数用户不需要在应用程序外导航，因此，如果离开应用，用户可能永远都不会返回它，实质上是终止了订婚。

iOS 9 改进使用户可以通过 Safari 页面左上角提供的 "后退" 按钮轻松返回到应用。

## <a name="app-transport-security"></a>应用传输安全性

应用传输安全性或*ATS*是由 Apple 在 iOS 9 中引入的，以确保所有 internet 通信都符合安全连接最佳实践。

有关 ATS 的详细信息，包括如何在应用中实现它，请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)指南。

## <a name="uiwebview-deprecated"></a>UIWebView （不推荐使用）

> [!IMPORTANT]
> `UIWebView` 已弃用。 使用此控件的应用程序将不会被[接受到2020年4月之前的应用商店，现有应用需要在12月2020之前将其删除](https://developer.apple.com/news/?id=12232019b)。
>
> [Apple 的 `UIWebView` 文档](https://developer.apple.com/documentation/uikit/uiwebview)建议应用应改用[`WKWebView`](#wkwebview) 。

> [!IMPORTANT]
> 使用 Xamarin.Forms 时，若要查看关于 `UIWebView` 的弃用警告 (ITMS-90809) 的相关资源，请参阅 [Xamarin.Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809) 文档。

`UIWebView` 是 Apple 在您的应用程序中提供 web 内容的传统方法。 它已在 iOS 2.0 中发布，并已在8.0 后弃用。

若要将 UIWebView 添加到你的 Xamarin iOS 应用，请使用以下代码：

```csharp
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://docs.microsoft.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

这会生成以下 web 视图：

[![ScalesPagesToFit 的效果](webview-images/webview.png)](webview-images/webview.png#lightbox)

## <a name="related-links"></a>相关链接

- [Webview （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
