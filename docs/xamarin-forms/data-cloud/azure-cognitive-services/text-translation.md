---
title: 使用 Translator API 的文本翻译
description: 可以使用 Microsoft Translator API 翻译语音和文本通过 REST API。 本文介绍如何使用 Microsoft 文本翻译 API 将文本从一种语言到 Xamarin.Forms 应用程序中的另一个翻译。
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 841b1d4abab5e4c09249174b221da20794771a86
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725566"
---
# <a name="text-translation-using-the-translator-api"></a>使用 Translator API 的文本翻译

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Translator API 可用于通过 REST API 翻译语音和文本。本文介绍如何使用 Microsoft 文本翻译 API 将 Xamarin 中的文本从一种语言翻译成另一种语言。_

## <a name="overview"></a>概述

Translator API 有两个组件：

- 文本翻译 REST API 将文本从一种语言翻译成另一种语言的文本。 API 会自动检测已发送之前将其转换的文本的语言。
- 语音翻译 REST API 进行转录语音从一种语言到另一种语言的文本。 API 还集成了朗读已翻译的文本返回的文本到语音转换功能。

本文重点介绍翻译文本从一种语言到另一个使用文本翻译 API。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

若要使用文本翻译 API，必须获取 API 密钥。 此操作可在[如何注册 Microsoft 文本翻译 API](/azure/cognitive-services/translator/translator-text-how-to-signup/)中获得。

有关 Microsoft 文本翻译 API 的详细信息，请参阅[文本翻译 API 文档](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>身份验证

对文本翻译 API 所做的每个请求都需要一个 JSON Web 令牌（JWT）访问令牌，该令牌可从 "认知服务令牌" 服务的 `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`获取。 可以通过对令牌服务发出 POST 请求来获得令牌，并指定包含 API 密钥作为其值的 `Ocp-Apim-Subscription-Key` 标头。

下面的代码示例演示如何请求访问令牌的令牌服务：

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

返回的访问令牌，它为 Base64 文本时，有 10 分钟的到期时间。 因此，示例应用程序将续订访问令牌每隔 9 分钟。

必须在每个文本翻译 API 调用中指定访问令牌，因为 `Authorization` 标头以字符串 `Bearer`为前缀，如以下代码示例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

有关认知服务令牌服务的详细信息，请参阅[身份验证](/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="performing-text-translation"></a>执行文本转换

可以通过对 `https://api.microsofttranslator.com/v2/http.svc/translate`的 `translate` API 发出 GET 请求来实现文本转换。 在示例应用程序中，`TranslateTextAsync` 方法调用文本转换过程：

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

`TranslateTextAsync` 方法将生成请求 URI，并从令牌服务检索访问令牌。 然后，将文本翻译请求发送到 `translate` API，该 API 返回包含结果的 XML 响应。 分析 XML 响应，并转换结果返回到调用方法以显示。

有关文本翻译 REST Api 的详细信息，请参阅[文本翻译 API](/azure/cognitive-services/translator/reference/v3-0-reference)。

### <a name="configuring-text-translation"></a>配置文本翻译

文本翻译过程可通过指定 HTTP 查询参数进行配置：

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

此方法设置要转换为文本的语言和要转换的文本。 有关 Microsoft Translator 支持的语言的列表，请参阅[microsoft 文本翻译 API 中支持的语言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果应用程序需要知道文本在哪种语言，则可以调用 `Detect` API 来检测文本字符串的语言。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync` 方法向文本转换发出 GET 请求 REST API 并返回响应：

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

此方法通过将访问令牌添加到 `Authorization` 标头来生成 GET 请求，并以字符串 `Bearer`为前缀。 然后，将 GET 请求发送到 `translate` API，并将请求 URL 指定为要转换的文本和文本转换为的语言。 然后将对响应进行读取，并将其返回给调用的方法。

如果请求有效，则 `translate` API 将在响应中发送 HTTP 状态代码200（正常），这表明请求已成功，并且请求的信息在响应中。 有关可能的错误响应的列表，请参阅[GET 翻译](/azure/cognitive-services/translator/reference/v3-0-translate)中的响应消息。

### <a name="processing-the-response"></a>处理响应

以 XML 格式返回 API 响应。 下面的 XML 数据显示了典型的成功响应消息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

在示例应用程序中，XML 响应分析为 `XDocument` 实例，并将 XML 根值返回到用于显示的调用方法，如以下屏幕截图所示：

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>摘要

本文介绍了如何使用 Microsoft 文本翻译 API 将从一种语言的文本翻译到 Xamarin.Forms 应用程序中的另一种语言的文本。 除了将文本翻译，Microsoft Translator API 还可以到另一种语言的文本转录从一种语言的语音。

## <a name="related-links"></a>相关链接

- [文本翻译 API 文档](/azure/cognitive-services/translator/)
- [使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 认知服务（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [文本翻译 API](/azure/cognitive-services/translator/reference/v3-0-reference)
