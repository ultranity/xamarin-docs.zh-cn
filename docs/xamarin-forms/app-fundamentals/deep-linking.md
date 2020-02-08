---
title: 应用程序索引和深层链接
description: 本文介绍如何使用应用程序索引和深层链接使 Xamarin.Forms 应用程序内容可在 iOS 和 Android 设备上进行搜索。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: fcd8333a0623058fceb486183ddb995e85eaf18a
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940324"
---
# <a name="application-indexing-and-deep-linking"></a>应用程序索引和深层链接

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)

_应用程序索引使得用了几次之后可能被忘记的应用程序出现在搜索结果中从而不会被忘。深层链接使应用程序响应包含应用程序数据的搜索结果，通常是通过导航到引用自深层链接的页面来实现的。本文介绍如何使用应用程序索引和深层链接使 Xamarin.Forms 应用程序内容可在 iOS 和 Android 设备上进行搜索。_

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Xamarin.Forms 和 Azure 视频的深层链接**

Xamarin.Forms 应用程序索引和深层链接提供一个 API，在用户浏览应用程序时发布元数据进行应用程序索引。 然后索引的内容可在 Spotlight 搜索、Google 搜索或 Web 搜索中进行搜索。 点击包含深层链接的搜索结果将触发一个事件，该事件可由应用程序进行处理且通常用于导航到引用自深层链接的页面。

示例演示了一个待办事项列表应用程序，其中数据存储于本地 SQLite 数据库，如以下屏幕截图所示：

![](deep-linking-images/screenshots.png "TodoList Application")

用户创建的每个 `TodoItem` 实例都进行了索引。 然后可使用特定于平台的搜索来查找来自应用程序的索引数据。 当用户点击应用程序的搜索结果项时，将启动该应用程序，并导航到 `TodoItemPage`，还将显示引用自深层链接的 `TodoItem`。

有关使用 SQLite 数据库的详细信息，请参阅 [Xamarin.Forms 本地数据库](~/xamarin-forms/data-cloud/data/databases.md)。

> [!NOTE]
> Xamarin.Forms 应用程序索引和深度链接功能仅适用于 iOS 和 Android 平台，并且分别至少需要 iOS 9 和 API 23。

## <a name="setup"></a>安装

以下部分提供在 iOS 和 Android 平台上使用此功能的其他设置说明。

### <a name="ios"></a>iOS

在 iOS 平台上，确保 iOS 平台项目将 Entitlements.plist 文件设置为自定义的授权文件以对捆绑内容进行签名。

使用 iOS 通用链接：

1. 使用 `applinks` 项向应用添加关联域授权密钥，包括应用将支持的所有域。
1. 向网站添加 Apple 应用站点关联文件。
1. 将 `applinks` 项添加到 Apple 应用站点关联文件。

有关详细信息，请参阅 developer.apple.com 上的[允许应用和网站链接到内容](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content)。

### <a name="android"></a>Android

要在 Android 平台上使用应用程序索引和深度链接功能，必须满足一系列先决条件：

1. 应用程序版本必须是 Google Play 上的实时版本。
1. 必须针对 Google 开发人员控制台中的应用程序注册配套网站。 应用程序与网站相关联后，可以索引同时适用于网站和应用程序的 URL，然后可在搜索结果中使用该 URL。 有关详细信息，请参阅 Google 网站上的 [Google 搜索中的应用索引](https://support.google.com/googleplay/android-developer/answer/6041489)。
1. 应用程序必须支持 `MainActivity` 类上的 HTTP URL 意向，它将告知应用程序索引应用程序可以响应哪些类型的 URL 数据方案。 有关详细信息，请参阅[配置意向筛选器](~/android/platform/app-linking.md#configure-intent-filter)。

满足这些先决条件后，需要进行以下其他设置才能在 Android 平台上使用 Xamarin.Forms 应用程序索引和深层链接：

1. 将 [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) NuGet 包安装到 Android 应用程序项目。
1. 在 MainActivity.cs 文件中，添加声明以使用 `Xamarin.Forms.Platform.Android.AppLinks` 命名空间。
1. 在 MainActivity.cs 文件中，添加声明以使用 `Firebase` 命名空间。
1. 在 Web 浏览器中，通过 [Firebase 控制台](https://console.firebase.google.com/)创建新项目。
1. 在 Firebase 控制台中，将 Firebase 添加到 Android 应用，并输入所需的数据。
1. 下载产生的 google-services.json 文件。
1. 将 google-services.json 文件添加到 Android 项目的根目录，然后将其生成操作设置为 GoogleServicesJson。
1. 在 `MainActivity.OnCreate` 重写中，在 `Forms.Init(this, bundle)` 下添加以下代码行：

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

将 google-services.json 添加到项目（并且设置了 GoogleServicesJson* 生成操作）时，生成过程将提取客户端 ID 和 API 密钥，然后将这些凭据添加到生成的清单文件。

> [!NOTE]
> 在本文中，术语“应用程序链接”和“深层链接”通常可以交换使用。 但是，在 Android 上，这些术语具有不同的含义。 在 Android 上，深层链接是一个意图筛选器，可让用户直接在应用中输入特定活动。 单击深层链接可能会打开一个消除歧义对话框，该对话框允许用户选择可以处理 URL 的多个应用程序之一。 Android 应用链接是基于网站 URL 的深层链接，该链接已被验证为属于网站。 单击应用链接会打开应用（如果已安装），而不会打开消除歧义对话框。

有关详细信息，请参阅 Xamarin 博客上的[深层链接内容与 Xamarin.Forms URL 导航](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)。

## <a name="indexing-a-page"></a>对页面进行索引

索引页面并将其公开到 Google 和 Spotlight 搜索的过程如下所示：

1. 创建包含索引页面所需元数据的 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry)，以及当用户在搜索结果中选择索引的内容时将返回的深层链接。
1. 注册 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例以索引它来进行搜索。

下面的代码示例演示如何创建 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例：

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

[`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例包含其值为索引页面并创建深层链接所必需的系列属性。 [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title)、[`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) 和 [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) 属性用于当索引的内容出现在搜索结果中时识别它。 将 [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 属性设置为 `true` 以指示当前正在查看索引的内容。 [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) 属性是包含返回到当前页面并显示当前 `TodoItem` 所必需的 `Uri`。 下面的示例显示示例应用程序的示例 `Uri`：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

此 `Uri` 包含启动 `deeplinking` 应用、导航到 `DeepLinking.TodoItemPage` 并显示 `ID` 为 2 的 `TodoItem` 所必需的所有信息。

## <a name="registering-content-for-indexing"></a>将内容注册为进行索引

创建 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例后，必须将它注册为进行索引以显示在搜索结果中。 这通过 [`RegisterLink`](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) 方法实现，如下面的代码示例所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

这将 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例添加到应用程序的 [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) 集合。

> [!NOTE]
> `RegisterLink` 方法还可用于更新已为页面进行过索引的内容。

将 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例注册为进行索引后，它可显示在搜索结果中。 下面的屏幕截图显示了出现在 iOS 平台上搜索结果中的索引的内容：

![](deep-linking-images/ios-search.png "Indexed Content in Search Results on iOS")

## <a name="de-registering-indexed-content"></a>取消注册索引的内容

[`DeregisterLink`](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) 方法用于从搜索结果中删除索引的内容，如下面的代码示例所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

这将从应用程序的 [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) 集合中删除 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例。

> [!NOTE]
> 在 Android 上，不能从搜索结果中删除索引的内容。

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>响应深层链接

当索引的内容出现在搜索结果中且被用户选择时，应用程序的 `App` 类将收到处理索引的内容中所含 `Uri` 的请求。 此请求可在 [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) 重写中进行处理，如下面的代码示例所示：

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

[`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) 方法检查收到的 `Uri` 是否为应用程序所需要，再将 `Uri` 解析为要导航到的页面及要传递到该页面的参数。 创建要导航到的页面的实例，并检索页面参数所表示的 `TodoItem`。 然后将要导航到的页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设为 `TodoItem`。 这可确保当 [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) 方法显示 `TodoItemPage` 时，它将显示其 `ID` 包含在深层链接中的 `TodoItem`。

## <a name="making-content-available-for-search-indexing"></a>使内容可供搜索索引使用

每次显示深层链接所表示的页面时，[`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 属性都将设置为 `true`。 在 iOS 和 Android 上，这使得 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例可供搜索索引使用，并且在 iOS 上，它还使 `AppLinkEntry` 实例可供 Handoff 使用。 有关 Handoff 的详细信息，请参阅 [Handoff 简介](~/ios/platform/handoff.md)。

下面的代码示例演示如何在 [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 重写中将 [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 属性设置为 `true`：

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

同样地，导航离开深层链接所表示的页面时，可将 [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 属性设置为 `false`。 在 iOS 和 Android 上，这将阻止为搜索索引宣传 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 实例，并且在 iOS 上，它还将阻止为 Handoff 宣传 `AppLinkEntry` 实例。 这可在 [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 重写中完成，如下面的代码示例所示：

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>向 Handoff 提供数据

在 iOS 上，索引页面时可以存储特定于应用程序的数据。 可通过将数据添加到 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合完成该操作，该集合是存储用于 Handoff 的键值对的 `Dictionary<string, string>`。 Handoff 是用户在一个设备上启动活动而在另一个设备上继续该活动的方式（由用户的 iCloud 帐户标识）。 下面的代码显示存储特定于应用程序的键值对的示例：

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

[`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合中所存储的值将存储于索引页的元数据中，并且当用户点击包含深层链接的搜索结果时（或当 Handoff 用于查看其他登录设备上的内容时）将还原这些值。

此外，可以指定以下项的值：

- `contentType` - 用以指定索引的内容的统一类型标识符的 `string`。 习惯上推荐将包含索引的内容的页面的类型名称用于此值。
- `associatedWebPage` - 表示当 Web 上可查看索引的内容时或当应用程序支持 Safari 的深层链接时将访问的网页的 `string`。
- `shouldAddToPublicIndex` - 值为 `true` 或 `false` 的 `string`，其控制是否将索引的内容添加到 Apple 的公有云索引，然后可向尚未在其 iOS 设备上安装该应用程序的用户展示该内容。 但是，只是因为已将内容设置为公共索引，并不意味着它将自动添加到 Apple 的公有云索引。 有关详细信息，请参阅[公共搜索索引](~/ios/platform/search/nsuseractivity.md)。 请注意，将个人数据添加到 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合时此项应设置为 `false`。

> [!NOTE]
> `KeyValues` 集合不在 Android 平台上使用。

有关 Handoff 的详细信息，请参阅 [Handoff 简介](~/ios/platform/handoff.md)。

## <a name="summary"></a>总结

本文介绍如何使用应用程序索引和深层链接使 Xamarin.Forms 应用程序内容可在 iOS 和 Android 设备上进行搜索。 应用程序索引使得用了几次之后可能被忘记的应用程序出现在搜索结果中从而不会被忘。

## <a name="related-links"></a>相关链接

- [深层链接（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)
- [iOS 搜索 API](~/ios/platform/search/index.md)
- [Android 6.0 中的应用链接](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
