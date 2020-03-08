---
title: 身份验证和授权
description: 本章介绍了 eShopOnContainers mobile 应用如何针对容器化微服务执行身份验证和授权。
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 528ccd66cc013f83752d93251cb9714115b29819
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915272"
---
# <a name="authentication-and-authorization"></a>身份验证和授权

身份验证是从用户获取标识凭据（例如用户名和密码）的过程，并根据机构验证这些凭据。 如果凭据有效，则提交凭据的实体被视为经过身份验证的标识。 身份验证后，授权过程会确定该标识是否有权访问给定的资源。

有许多种集成到 Xamarin.Forms 应用 ASP.NET MVC web 应用程序，包括使用 ASP.NET Core 标识时，例如 Microsoft、 Google、 外部身份验证提供程序与之通信的身份验证和授权Facebook 或 Twitter 和身份验证中间件。 EShopOnContainers 移动应用使用 IdentityServer 4 的容器化标识微服务执行身份验证和授权。 移动应用程序请求 IdentityServer 的安全令牌，用于对用户进行身份验证或访问资源。 要使 IdentityServer 代表用户颁发令牌，用户必须登录到 IdentityServer。 但是，IdentityServer 不提供用于身份验证的用户界面或数据库。 因此，在 eShopOnContainers 引用应用程序中，ASP.NET Core 标识用于此目的。

## <a name="authentication"></a>Authentication

当应用程序需要知道当前用户的身份时，需要进行身份验证。 ASP.NET Core 用于标识用户的主要机制是 ASP.NET Core 标识成员身份系统，它在由开发人员配置了数据存储中存储用户的信息。 通常，此数据存储将为 EntityFramework 存储区，但自定义商店或第三方包可用于将标识信息存储在 Azure 存储、Azure Cosmos DB 或其他位置。

有关身份验证方案，请使用本地用户数据存储区，并且保留之间通过 cookie （就是典型 ASP.NET MVC web 应用程序中） 发出的请求的标识信息，则 ASP.NET Core 标识为合适的解决方案。 但是，cookie 并非始终是保存和传输数据的自然方式。 例如，ASP.NET Core web 应用程序公开从移动应用程序访问的 RESTful 终结点通常需要使用持有者令牌身份验证，因为无法在此方案中使用 cookie。 但是，可以轻松检索持有者令牌，并将其包括在从移动应用发出的 web 请求的授权标头中。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>使用 IdentityServer 4 颁发持有者令牌

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4)是用于 ASP.NET Core 的开源 OpenID Connect 和 OAuth 2.0 framework，可用于许多身份验证和授权方案，包括为本地 ASP.NET Core 标识用户颁发安全令牌。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 非常相似，但具有不同的职责。

OpenID Connect 是位于 OAuth 2.0 协议顶层的身份验证层。 OAuth 2 是一种协议，它允许应用程序请求来自 security token service 的访问令牌，并使用这些令牌与 Api 通信。 此委托降低了客户端应用程序和 Api 的复杂性，因为身份验证和授权可以集中进行。

OpenID Connect 和 OAuth 2.0 的组合结合了身份验证和 API 访问的两个基本安全问题，IdentityServer 4 是这些协议的实现。

在使用直接客户端到微服务通信的应用程序（如 eShopOnContainers 引用应用程序）中，可使用专用身份验证微服务作为安全令牌服务（STS）来对用户进行身份验证，如图所示9-1。 有关直接的客户端到微服务通信的详细信息，请参阅[客户端和微服务之间的通信](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices)。

![](authentication-and-authorization-images/authentication.png "Authentication by a dedicated authentication microservice")

**图9-1：** 通过专用身份验证微服务进行身份验证

EShopOnContainers 移动应用与标识微服务通信，后者使用 IdentityServer 4 来执行身份验证，并使用 Api 的访问控制。 因此，移动应用从 IdentityServer 请求令牌，以便对用户进行身份验证或访问资源：

- 使用 IdentityServer 对用户进行身份验证时，移动应用会请求*标识*令牌，这表示身份验证过程的结果。 它至少包含用户的标识符，以及有关用户身份验证的方式和时间的信息。 它还可以包含其他标识数据。
- 使用 IdentityServer 访问资源时，移动应用会请求*访问*令牌，这允许访问 API 资源。 客户端请求访问令牌，并将其转发给 API。 访问令牌包含有关客户端和用户（如果有）的信息。 然后，Api 使用该信息授予对其数据的访问权限。

> [!NOTE]
> 必须先向 IdentityServer 注册客户端，然后才能请求令牌。

### <a name="adding-identityserver-to-a-web-application"></a>将 IdentityServer 添加到 Web 应用程序

为了使 ASP.NET Core web 应用程序使用 IdentityServer 4，必须将添加到 web 应用程序的 Visual Studio 解决方案。 有关详细信息，请参阅 IdentityServer 文档中的[概述](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html)。

一旦将 IdentityServer 包含在 web 应用程序的 Visual Studio 解决方案中，就必须将其添加到 web 应用程序的 HTTP 请求处理管道中，以便可以为 OpenID Connect 和 OAuth 2.0 终结点请求提供服务。 这是在 web 应用程序的 `Startup` 类的 `Configure` 方法中实现的，如下面的代码示例所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

对 web 应用程序的 HTTP 请求处理管道中的问题进行排序。 因此，必须在实现登录屏幕的 UI 框架之前将 IdentityServer 添加到管道。

### <a name="configuring-identityserver"></a>配置 IdentityServer

通过调用 `services.AddIdentityServer` 方法，应在 web 应用程序的 `Startup` 类的 `ConfigureServices` 方法中配置 IdentityServer，如下面的代码示例 eShopOnContainers reference 应用程序所示：

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

调用 `services.AddIdentityServer` 方法后，将调用其他流畅的 Api 来配置以下内容：

- 用于签名的凭据。
- 用户可能会请求访问的 API 和标识资源。
- 将连接到请求令牌的客户端。
- ASP.NET Core 标识。

> [!TIP]
> 动态加载 IdentityServer 4 配置。 IdentityServer 4 的 Api 允许从内存中的配置对象列表配置 IdentityServer。 在 eShopOnContainers 引用应用程序中，这些内存中集合硬编码到应用程序中。 但是，在生产方案中，可以从配置文件或从数据库动态加载它们。

有关将 IdentityServer 配置为使用 ASP.NET Core 标识的信息，请参阅 IdentityServer 文档中的[使用 ASP.NET Core 标识](https://identityserver4.readthedocs.io/en/latest/quickstarts/6_aspnet_identity.html)。

#### <a name="configuring-api-resources"></a>配置 API 资源

配置 API 资源时，`AddInMemoryApiResources` 方法需要 `IEnumerable<ApiResource>` 集合。 下面的代码示例演示在 eShopOnContainers 引用应用程序中提供此集合的 `GetApis` 方法：

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

此方法指定 IdentityServer 应保护订单和购物篮 Api。 因此，对这些 Api 进行调用时，将需要 IdentityServer 托管访问令牌。 有关 `ApiResource` 类型的详细信息，请参阅 IdentityServer 4 文档中的[API 资源](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html)。

#### <a name="configuring-identity-resources"></a>配置标识资源

配置标识资源时，`AddInMemoryIdentityResources` 方法需要 `IEnumerable<IdentityResource>` 集合。 标识资源是数据，例如用户 ID、名称或电子邮件地址。 每个标识资源都有一个唯一的名称，可向其分配任意声明类型，然后将其包含在用户的标识令牌中。 下面的代码示例演示在 eShopOnContainers 引用应用程序中提供此集合的 `GetResources` 方法：

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

OpenID Connect 规范指定某些[标准标识资源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低要求是提供支持来为用户发出唯一 ID。 这是通过公开 `IdentityResources.OpenId` 标识资源来实现的。

> [!NOTE]
> `IdentityResources` 类支持 OpenID Connect 规范中定义的所有范围（openid、电子邮件、配置文件、电话和地址）。

IdentityServer 还支持定义自定义标识资源。 有关详细信息，请参阅 IdentityServer 文档中的[定义自定义标识资源](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources)。 有关 `IdentityResource` 类型的详细信息，请参阅 IdentityServer 4 文档中的[标识资源](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html)。

#### <a name="configuring-clients"></a>配置客户端

客户端是可以从 IdentityServer 请求令牌的应用程序。 通常，必须为每个客户端至少定义以下设置：

- 唯一的客户端 ID。
- 允许与令牌服务交互（称为授予类型）。
- 标识和访问令牌发送到的位置（称为重定向 URI）。
- 允许客户端访问的资源列表（称为范围）。

在配置客户端时，`AddInMemoryClients` 方法需要 `IEnumerable<Client>` 集合。 下面的代码示例演示 `GetClients` 方法中的 eShopOnContainers 移动应用的配置，该方法在 eShopOnContainers 引用应用程序中提供此集合：

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

此配置指定以下属性的数据：

- `ClientId`：客户端的唯一 ID。
- `ClientName`：客户端显示名称，用于记录和同意屏幕。
- `AllowedGrantTypes`：指定客户端想要如何与 IdentityServer 交互。 有关详细信息，请参阅[配置身份验证流](#configuring_the_authentication_flow)。
- `ClientSecrets`：指定从令牌终结点请求令牌时使用的客户端机密凭据。
- `RedirectUris`：指定为其返回令牌或授权代码的允许 Uri。
- `RequireConsent`：指定是否需要同意屏幕。
- `RequirePkce`：指定使用授权代码的客户端是否必须发送证明密钥。
- `PostLogoutRedirectUris`：指定注销后允许重定向到的 Uri。
- `AllowedCorsOrigins`：指定客户端的源，以便 IdentityServer 可以允许来自源的跨源调用。
- `AllowedScopes`：指定客户端有权访问的资源。 默认情况下，客户端不能访问任何资源。
- `AllowOfflineAccess`：指定客户端是否可以请求刷新令牌。

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>配置身份验证流

可以通过在 `Client.AllowedGrantTypes` 属性中指定 grant 类型来配置客户端与 IdentityServer 之间的身份验证流。 OpenID Connect 和 OAuth 2.0 规范定义了多个身份验证流，其中包括：

- 隐式。 此流针对基于浏览器的应用程序进行了优化，并且应该用于仅用户身份验证或身份验证和访问令牌请求。 所有令牌都是通过浏览器传输的，因此不允许使用刷新令牌等高级功能。
- 授权代码。 通过此流，可以在后端通道上检索令牌，而不是浏览器前端通道，同时还支持客户端身份验证。
- 混合： 此流是隐式和授权代码授予类型的组合。 标识令牌通过浏览器通道传输，包含签名协议响应以及其他项目，如授权代码。 成功验证响应后，应使用后信道来检索访问和刷新令牌。

> [!TIP]
> 使用混合身份验证流。 混合身份验证流可缓解适用于浏览器通道的大量攻击，对于需要检索访问令牌（并且可能会刷新令牌）的本机应用程序，建议使用此流。

有关身份验证流的详细信息，请参阅 IdentityServer 4 文档中的[授予类型](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html)。

### <a name="performing-authentication"></a>执行身份验证

要使 IdentityServer 代表用户颁发令牌，用户必须登录到 IdentityServer。 但是，IdentityServer 不提供用于身份验证的用户界面或数据库。 因此，在 eShopOnContainers 引用应用程序中，ASP.NET Core 标识用于此目的。

EShopOnContainers 移动应用通过混合身份验证流进行身份验证，如图9-2 所示。

![](authentication-and-authorization-images/sign-in.png "High-level overview of the sign-in process")

**图9-2：** 登录过程的高级概述

`<base endpoint>:5105/connect/authorize`登录请求。 身份验证成功后，IdentityServer 将返回一个身份验证响应，其中包含授权代码和标识令牌。 然后，授权代码将发送到 `<base endpoint>:5105/connect/token`，这将使用访问令牌和刷新令牌进行响应。

EShopOnContainers 移动应用通过将请求发送到 `<base endpoint>:5105/connect/endsession`，并使用其他参数来注销 IdentityServer。 注销发生后，IdentityServer 会通过将注销后重定向 URI 发送回移动应用来做出响应。 图9-3 说明了此过程。

![](authentication-and-authorization-images/sign-out.png "High-level overview of the sign-out process")

**图9-3：** 注销过程的高级概述

在 eShopOnContainers 移动应用中，与 IdentityServer 的通信由 `IdentityService` 类执行，该类实现 `IIdentityService` 接口。 此接口指定实现类必须提供 `CreateAuthorizationRequest`、`CreateLogoutRequest`和 `GetTokenAsync` 方法。

#### <a name="signing-in"></a>登录

当用户点击 `LoginView`上的 "**登录**" 按钮时，将执行 `LoginViewModel` 类中的 `SignInCommand`，进而执行 `SignInAsync` 方法。 下面的代码示例演示此方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

此方法调用 `IdentityService` 类中的 `CreateAuthorizationRequest` 方法，如以下代码示例所示：

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.Callback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

此方法将创建 IdentityServer 的[授权终结点](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)的 URI 以及所需的参数。 授权终结点在公开为用户设置的基本终结点端口5105上 `/connect/authorize`。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 通过为代码交换（PKCE）扩展实现 OAuth，降低了 eShopOnContainers 移动应用的受攻击面。 PKCE 可防止在其被拦截时使用授权代码。 这是通过生成机密验证程序的客户端实现的，它是在授权请求中传递的哈希值，并在兑换授权代码时提供了哈希。 有关 PKCE 的详细信息，请参阅 Internet 工程任务组网站上的[OAuth 公共客户端代码交换的证明密钥](https://tools.ietf.org/html/rfc7636)。

返回的 URI 存储在 `LoginViewModel` 类的 `LoginUrl` 属性中。 `IsLogin` 属性变为 `true`时，`LoginView` 中的[`WebView`](xref:Xamarin.Forms.WebView)将变为可见。 `WebView` 数据将其[`Source`](xref:Xamarin.Forms.WebView.Source)属性绑定到 `LoginViewModel` 类的 `LoginUrl` 属性，因此当 `LoginUrl` 属性设置为 IdentityServer 的授权终结点时，会向 IdentityServer 发出登录请求。 当 IdentityServer 收到此请求并且用户未通过身份验证时，`WebView` 将重定向到已配置的登录页，如图9-4 所示。

![](authentication-and-authorization-images/login.png "Login page displayed by the WebView")

**图9-4：** Web 视图显示的登录页

登录完成后， [`WebView`](xref:Xamarin.Forms.WebView)将被重定向到返回的 URI。 此 `WebView` 导航将导致执行 `LoginViewModel` 类中的 `NavigateAsync` 方法，如以下代码示例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

此方法分析返回 URI 中包含的身份验证响应，并提供有效的授权代码，并向 IdentityServer 的[令牌终结点](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)发出请求，传递授权代码、PKCE 密钥验证程序和其他所需的参数。 令牌终结点位于公开为用户设置的基本终结点端口5105上 `/connect/token`。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!TIP]
> 验证返回的 Uri。 尽管 eShopOnContainers 移动应用程序不会验证返回 URI，但最佳做法是验证返回 URI 是否引用了已知位置，以防止开放重定向攻击。

如果令牌终结点收到有效的授权代码和 PKCE 机密验证程序，则它将使用访问令牌、标识令牌和刷新令牌进行响应。 然后，访问令牌（允许访问 API 资源）和标识令牌存储为应用程序设置，并执行页面导航。 因此，eShopOnContainers 移动应用中的整体效果如下所示：如果用户能够使用 IdentityServer 成功进行身份验证，则会导航到 "`MainView`" 页，该页面是将 `CatalogView` 显示为其选定选项卡的[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

有关页面导航的信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 有关[`WebView`](xref:Xamarin.Forms.WebView)导航如何导致执行视图模型方法的信息，请参阅[使用行为调用导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 有关应用程序设置的信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 当应用配置为使用 `SettingsView`中的模拟服务时，eShopOnContainers 还允许进行模拟登录。 在此模式下，应用不会与 IdentityServer 通信，而是允许用户使用任何凭据进行登录。

#### <a name="signing-out"></a>注销

当用户点击 `ProfileView`中的 "**注销**" 按钮时，将执行 `ProfileViewModel` 类中的 `LogoutCommand`，进而执行 `LogoutAsync` 方法。 此方法执行到 `LoginView` 页的页导航，并将 `LogoutParameter` 实例集作为参数传递给 `true`。 有关在页面导航期间传递参数的详细信息，请参阅[在导航过程中传递参数](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

创建并导航到视图时，将执行视图的关联视图模型的 `InitializeAsync` 方法，然后执行 `LoginViewModel` 类的 `Logout` 方法，如以下代码示例所示：

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

此方法调用 `IdentityService` 类中的 `CreateLogoutRequest` 方法，将从应用程序设置检索的标识令牌作为参数传递。 有关应用程序设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下面的代码示例说明 `CreateLogoutRequest` 方法：

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

此方法用所需的参数创建 IdentityServer 的[结束会话终结点](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)的 URI。 最终会话终结点在公开为用户设置的基本终结点端口5105上 `/connect/endsession`。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

返回的 URI 存储在 `LoginViewModel` 类的 `LoginUrl` 属性中。 当 `true``IsLogin` 属性时，`LoginView` 中的[`WebView`](xref:Xamarin.Forms.WebView)可见。 `WebView` 数据将其[`Source`](xref:Xamarin.Forms.WebView.Source)属性绑定到 `LoginViewModel` 类的 `LoginUrl` 属性，因此当 `LoginUrl` 属性设置为 IdentityServer 的结束会话终结点时，会向 IdentityServer 发出注销请求。 当 IdentityServer 收到此请求时，如果用户已登录，则会出现注销。 身份验证会跟踪与由从 ASP.NET Core cookie 身份验证中间件的 cookie。 因此，注销 IdentityServer 将删除身份验证 cookie，并将注销后的重定向 URI 发送回客户端。

在移动应用中， [`WebView`](xref:Xamarin.Forms.WebView)将重定向到注销后重定向 URI。 此 `WebView` 导航将导致执行 `LoginViewModel` 类中的 `NavigateAsync` 方法，如以下代码示例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

此方法会从应用程序设置中清除标识令牌和访问令牌，并将 `IsLogin` 属性设置为 `false`，这将导致 `LoginView` 页上的[`WebView`](xref:Xamarin.Forms.WebView)变得不可见。 最后，`LoginUrl` 属性设置为 IdentityServer 的[授权终结点](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)的 URI （具有所需的参数），以便在下次用户启动登录时进行准备。

有关页面导航的信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 有关[`WebView`](xref:Xamarin.Forms.WebView)导航如何导致执行视图模型方法的信息，请参阅[使用行为调用导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 有关应用程序设置的信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 当应用配置为在 SettingsView 中使用模拟服务时，eShopOnContainers 还允许模拟注销。 在此模式下，应用不与 IdentityServer 通信，而是从应用程序设置中清除任何存储的令牌。

<a name="authorization" />

## <a name="authorization"></a>授权

身份验证后，ASP.NET Core web Api 通常需要授予访问权限，这样，服务以使 Api 可供某些经过身份验证的用户，而不是为全部。

将访问限制为 ASP.NET Core MVC 路由可以通过将 Authorize 属性应用于控制器或操作，这可以限制访问控制器或操作到身份验证的用户，如下面的代码示例中所示：

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

如果未经授权的用户尝试访问标记为 `Authorize` 属性的控制器或操作，则 MVC 框架将返回401（未授权） HTTP 状态代码。

> [!NOTE]
> 可以在 `Authorize` 特性上指定参数，以将 API 限制到特定用户。 有关详细信息，请参阅[授权](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以集成到授权工作流，以便它提供控制授权。 此方法如图9-5 所示。

![](authentication-and-authorization-images/authorization.png "Authorization by access token")

**图9-5：** 按访问令牌授权

EShopOnContainers 移动应用与身份微服务通信，并请求访问令牌作为身份验证过程的一部分。 然后将访问令牌转发到由排序和购物篮微服务公开的 Api，作为访问请求的一部分。 访问令牌包含有关客户端和用户的信息。 然后，Api 使用该信息授予对其数据的访问权限。 有关如何配置 IdentityServer 以保护 Api 的信息，请参阅[配置 Api 资源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>配置 IdentityServer 以执行授权

若要执行 IdentityServer 授权，必须将其授权中间件添加到 web 应用程序的 HTTP 请求管道。 中间件添加到 web 应用程序的 `Startup` 类的 `ConfigureAuth` 方法中，该方法是从 `Configure` 方法调用的，并在以下 eShopOnContainers 引用应用程序的代码示例中演示：

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

此方法可确保仅可使用有效的访问令牌访问 API。 中间件将验证传入的令牌，以确保它是从受信任的颁发者发送的，并验证令牌是否有效，以便与接收它的 API 一起使用。 因此，浏览到订购或购物篮控制器将返回一个401（未授权） HTTP 状态代码，指出需要访问令牌。

> [!NOTE]
> 在将 `app.UseMvc()` 或 `app.UseMvcWithDefaultRoute()`添加 MVC 之前，必须先将 IdentityServer 的授权中间件添加到 web 应用程序的 HTTP 请求管道。

### <a name="making-access-requests-to-apis"></a>向 Api 发出访问请求

向订购和购物篮微服务发出请求时，必须在请求中包含访问令牌（在身份验证过程中从 IdentityServer 获取），如以下代码示例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

访问令牌将存储为应用程序设置，并从特定于平台的存储检索，并包含在 `OrderService` 类中对 `GetOrderAsync` 方法的调用中。

同样，将数据发送到 IdentityServer 保护的 API 时，必须包含访问令牌，如下面的代码示例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

访问令牌从平台特定的存储检索，并包含在 `BasketService` 类中对 `UpdateBasketAsync` 方法的调用中。

EShopOnContainers 移动应用中的 `RequestProvider` 类使用 `HttpClient` 类向 eShopOnContainers 引用应用程序所公开的 RESTful Api 发出请求。 向需要授权的订购和购物篮 Api 发出请求时，请求中必须包含有效的访问令牌。 这是通过将访问令牌添加到 `HttpClient` 实例的标头来实现的，如下面的代码示例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`HttpClient` 类的 `DefaultRequestHeaders` 属性公开随每个请求发送的标头，并将访问令牌添加到以字符串 `Bearer`为前缀的 `Authorization` 标头。 将请求发送到 RESTful API 时，将提取并验证 `Authorization` 标头的值，以确保它是从受信任的颁发者发送的，并用于确定用户是否有权调用接收它的 API。

有关 eShopOnContainers 移动应用程序如何进行 web 请求的详细信息，请参阅[访问远程数据](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>总结

可以通过许多方法将身份验证和授权集成到与 ASP.NET MVC Web 应用程序进行通讯的 Xamarin.Forms 应用中， EShopOnContainers 移动应用使用 IdentityServer 4 的容器化标识微服务执行身份验证和授权。 IdentityServer 是一种适用于 ASP.NET Core 的开源 OpenID Connect 和 OAuth 2.0  框架，它与 ASP.NET Core Identity 集成以执行持有者令牌身份验证。

移动应用程序请求 IdentityServer 的安全令牌，用于对用户进行身份验证或访问资源。 访问资源时，必须在请求中包含访问令牌，才能向需要授权的 Api 发出请求。 IdentityServer 的中间件将验证传入的访问令牌，以确保它们是从受信任的颁发者发送的，并且有效地用于接收这些令牌的 API。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
