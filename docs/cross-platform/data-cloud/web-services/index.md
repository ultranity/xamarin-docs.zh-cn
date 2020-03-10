---
title: Web 服务简介
description: 本指南演示如何使用不同的 web 服务技术。 涉及的主题包括与 REST 服务、SOAP 服务和 Windows Communication Foundation 服务通信。
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: ebd7cad9ef33a44dbc7aa469bb4e866bdfea2e61
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915392"
---
# <a name="introduction-to-web-services"></a>Web 服务简介

_本指南演示如何使用不同的 web 服务技术。涉及的主题包括与 REST 服务、SOAP 服务和 Windows Communication Foundation 服务通信。_

若要正常运行，许多移动应用程序都依赖于云，因此，将 web 服务集成到移动应用程序是一种常见方案。 Xamarin 平台支持使用不同的 web 服务技术，并包括对使用 RESTful、.ASMX 和 Windows Communication Foundation （WCF）服务的内置和第三方支持。

对于使用 Xamarin. Forms 的客户，可以使用[Xamarin Web Services](~/xamarin-forms/data-cloud/index.yml)文档中的每种技术来了解完整的示例。

> [!IMPORTANT]
> 在 iOS 9 中，应用传输安全（ATS）强制实施 internet 资源（如应用的后端服务器）与应用之间的安全连接，从而防止敏感信息的意外泄露。
> 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。

如果无法为 internet 资源使用 `HTTPS` 协议和安全通信，则可以选择退出 ATS。 这可以通过更新应用的**info.plist**文件来实现。 有关详细信息，请参阅[应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="rest"></a>REST

具象状态传输 (REST) 是用于构建 web 服务的体系结构样式。 REST 请求是通过发出 HTTP 使用 web 浏览器使用检索 web 页面，并将数据发送到服务器的同一 HTTP 谓词。 谓词是：

- **GET** –此操作用于从 web 服务检索数据。
- **POST** –此操作用于在 web 服务中创建新的数据项。
- **PUT** –此操作用于更新 web 服务上的数据项。
- **修补程序**–此操作用于通过描述有关应如何修改项的一组说明来更新 web 服务上的数据项。 在示例应用程序不使用此谓词。
- **删除**–此操作用于删除 web 服务上的数据项。

遵守 REST 的 Api 被称为 RESTful Api，并使用定义的 web 服务：

- 一个基 URI。
- HTTP 方法，如 GET、 POST、 PUT、 PATCH 或 DELETE。
- 数据，如 JavaScript 对象表示法 (JSON) 媒体类型。

REST 的简单性已帮助使其用于访问移动应用程序中的 web 服务的主要方法。

## <a name="consuming-rest-services"></a>使用 REST 服务

有许多可用于使用 REST 服务的库和类，以下小节讨论了这些库和类。 有关使用 REST 服务的详细信息，请参阅[使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)。

### <a name="httpclient"></a>HttpClient

[MICROSOFT HTTP 客户端库](https://www.nuget.org/packages/Microsoft.Net.Http)提供了 `HttpClient` 类，该类用于通过 HTTP 发送和接收请求。 它提供用于发送 HTTP 请求以及从 URI 标识的资源接收 HTTP 响应的功能。 每个请求将作为异步操作发送。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`HttpResponseMessage` 类表示发出 HTTP 请求之后从 web 服务接收的 HTTP 响应消息。 它包含有关响应的信息，包括状态代码、标头和正文。 `HttpContent` 类表示 HTTP 正文和内容标头，如 `Content-Type` 和 `Content-Encoding`。 可以使用任何 `ReadAs` 方法读取内容，例如 `ReadAsStringAsync` 和 `ReadAsByteArrayAsync`，具体取决于数据的格式。

有关 `HttpClient` 类的详细信息，请参阅[创建 HTTPClient 对象](~/xamarin-forms/data-cloud/web-services/rest.md)。

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

调用具有 `HTTPWebRequest` 的 web 服务涉及：

- 为特定 URI 创建请求实例。
- 设置请求实例的各种 HTTP 属性。
- 正在从请求中检索 `HttpWebResponse`。
- 读取响应中的数据。

例如，以下代码从医学的美国国家标准 web 服务库中检索数据：

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

上面的示例创建了一个 `HttpWebRequest`，它将返回设置为 JSON 格式的数据。 数据在 `HttpWebResponse`中返回，可以从该获取用于读取数据的 `StreamReader`。

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

使用 REST 服务的另一种方法是使用[RestSharp](http://restsharp.org/)库。 RestSharp 封装 HTTP 请求，包括支持以原始字符串内容或反序列化C#对象的形式检索结果。 例如，下面的代码对美国国家标准 web 服务库发出请求，并以 JSON 格式字符串的形式检索结果：

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` 是一种方法，它将从 `RestSharp.RestResponse.Content` 属性获取原始 JSON 字符串，并将其转换C#为对象。 本文后面将讨论从 web 服务返回的反序列化数据。

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

除了在 Mono 基类库（BCL）中提供的类（如 `HttpWebRequest`）以及第三方C#库（如 RestSharp），还可以使用 web 服务。 例如，在 iOS 中，可以使用 `NSUrlConnection` 和 `NSMutableUrlRequest` 类。

下面的代码示例演示如何使用 iOS 类调用美国国家标准 web 服务库：

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

通常，用于使用 web 服务的特定于平台的类应限制为将本机代码移植到C#的方案。 在可能的情况下，web 服务访问代码应该是可移植的，以便它可以跨平台共享。

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

用于调用 web 服务的另一种方法是[服务堆栈](https://servicestack.net)库。 例如，下面的代码演示如何使用服务堆栈的 `IServiceClient.GetAsync` 方法发出服务请求：

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> 尽管 ServiceStack 和 RestSharp 之类的工具可以轻松调用和使用 REST 服务，但有时使用不符合标准_DataContract_序列化约定的 XML 或 JSON 是非常简单的。 如有必要，请使用下面讨论的 ServiceStack 库来调用请求并显式处理适当的序列化。

<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>使用 RESTful 数据

RESTful web 服务通常使用 JSON 消息来将数据返回到客户端。 JSON 是一种基于文本的数据交换格式，可产生精简的负载，从而降低发送数据时的带宽需求。 在本部分中，将检查在 JSON 和纯 XML （POX）中使用 RESTful 响应的机制。

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Xamarin 平台随附了对 JSON 的支持。 通过使用 `JsonObject`，可以检索结果，如以下代码示例所示：

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

不过，请注意，`System.Json` 工具会将完整的数据加载到内存中，这一点很重要。

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

[Newtonsoft.json JSON.NET 库](https://www.newtonsoft.com/json)是一个广泛使用的库，用于序列化和反序列化 JSON 消息。 下面的代码示例演示如何使用 JSON.NET 将 JSON 消息反序列化为C#对象：

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack 是一个 JSON 序列化库，旨在使用 ServiceStack 库。 下面的代码示例演示如何使用 `ServiceStack.Text.JsonObject`分析 JSON：

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

使用基于 XML 的 REST web 服务时，LINQ to XML 可用于分析 XML 并以内联方式填充C#对象，如下面的代码示例所示：

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>ASP.NET Web 服务（.ASMX）

使用该功能，可以生成使用简单对象访问协议（SOAP）发送消息的 web 服务。 SOAP 是一种独立于平台的和独立于语言的构建和访问 web 服务协议。 一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。

SOAP 消息是包含以下元素的 XML 文档：

- 一个名为 "*信封*" 的根元素，它将 XML 文档标识为 SOAP 消息。
- 一个可选的*标头*元素，其中包含应用程序特定的信息，例如身份验证数据。 如果*标头*元素存在，则它必须是*信封*元素的第一个子元素。
- 一个必需的*Body*元素，其中包含用于接收方的 SOAP 消息。
- 用于指示错误消息的可选*错误*元素。 如果出现*错误*元素，则它必须是*Body*元素的子元素。

SOAP 可以在许多传输协议，包括 HTTP、 SMTP、 TCP 和 UDP 上操作。 但是，一个 ASMX 服务可以仅通过 HTTP 操作。 Xamarin 平台支持标准 SOAP 1.1 实现通过 HTTP，并且这包括对很多标准的 ASMX 服务配置的支持。

### <a name="generating-a-proxy"></a>生成代理

必须生成*代理*才能使用某个 .asmx 服务，这允许应用程序连接到该服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 此元数据公开为 web 服务生成的 Web 服务描述语言（WSDL）文档。 代理是使用 Visual Studio for Mac 或 Visual Studio 生成的，以便将 web 服务的 web 引用添加到特定于平台的项目。

Web 服务 URL 可以是可通过 `file:///` 路径前缀访问的托管远程源或本地文件系统资源，例如：

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "The web service URL can either be a hosted remote source or local file system resource accessible via the file path prefix")](images/add-webreference-dialog.png#lightbox)

这会在项目的 "Web" 或 "服务引用" 文件夹中生成代理。 由于代理是生成代码，因此不应进行修改。

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>手动将代理添加到项目

如果有一个使用兼容工具生成的现有代理，则当作为项目的一部分包含时，可以使用此输出。 在 Visual Studio for Mac 中，使用 "**添加文件 ...** " 用于添加代理的菜单选项。 此外，这需要使用 "**添加引用 ...** " 来显式引用*system.web* 。 对话框中的字段。

### <a name="consuming-the-proxy"></a>使用代理

生成的代理类提供用于使用 web 服务使用异步编程模型 (APM) 设计模式的方法。 在此模式下，异步操作将作为名为*BeginOperationName*和*EndOperationName*的两个方法来实现，该方法用于开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回实现 `IAsyncResult` 接口的对象。 调用*BeginOperationName*之后，应用程序可以继续在调用线程上执行指令，同时异步操作在线程池线程上发生。

对于每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取操作的结果。 *EndOperationName*的返回值与同步 web 服务方法返回的类型相同。 下面的代码示例演示了此示例：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

任务并行库（TPL）可以通过将异步操作封装在同一 `Task` 对象中来简化使用 APM begin/end 方法对的过程。 此封装由 `Task.Factory.FromAsync` 方法的多个重载提供。 此方法创建一个 `Task`，该在 `TodoService.BeginGetTodoItems` 方法完成后执行 `TodoService.EndGetTodoItems` 方法，并使用 `null` 参数指示没有数据传入 `BeginGetTodoItems` 委托。 最后，`TaskCreationOptions` 枚举的值指定应使用任务的创建和执行的默认行为。

有关 APM 的详细信息，请参阅 MSDN 上的[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和传统 .NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)。

有关使用 .ASMX 服务的详细信息，请参阅[使用 ASP.NET Web 服务（.asmx）](~/xamarin-forms/data-cloud/web-services/asmx.md)。

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF 是 Microsoft 的统一框架，用于生成面向服务的应用程序。 它允许开发人员构建安全、 可靠、 事务处理，且可互操作分布式应用程序。

WCF 描述了与各种不同的约定，其中包括以下服务：

- **数据协定**–定义构成消息中内容的基础的数据结构。
- **消息协定**–基于现有数据协定撰写消息。
- **故障协定**–允许指定自定义 SOAP 错误。
- **服务协定**–指定服务支持的操作，以及与每个操作交互所需的消息。 它们还指定可以与每个服务上的操作相关联的任何自定义错误行为。

ASP.NET Web 服务 (ASMX) 和 WCF 之间的差异，但务必要了解 WCF 支持的相同功能提供的 ASMX 服务 – 通过 HTTP 的 SOAP 消息。

> [!IMPORTANT]
> 使用 `BasicHttpBinding` 类通过 HTTP/HTTPS 将 Xamarin 平台支持限定为经过文本编码的 SOAP 消息。 此外，WCF 支持需要使用工具仅在 Windows 环境以生成代理中可用。

### <a name="generating-a-proxy"></a>生成代理

必须生成*代理*以使用 WCF 服务，该服务允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 此元数据中生成的 web 服务的 Web 服务描述语言 (WSDL) 文档的形式公开。 可以使用 Visual Studio 2017 中的 Microsoft WCF Web Service Reference Provider 生成代理，以将 Web 服务的服务引用添加到 .NET Standard 库中。

创建的代理帐户在 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider 的替代方法是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 有关详细信息，请参阅 " [svcutil.exe" 元数据实用工具（）](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>配置代理

在初始化期间，配置生成的代理通常会采用两个配置参数（具体取决于 SOAP 1.1/.ASMX 或 WCF）： `EndpointAddress` 和/或关联的绑定信息，如以下示例中所示：

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

使用绑定指定传输、 编码和协议详细信息所需的应用程序和服务彼此通信。 `BasicHttpBinding` 指定将通过 HTTP 传输协议发送文本编码的 SOAP 消息。 指定终结点地址可以连接到 WCF 服务的不同实例应用程序，前提是有多个已发布的实例。

### <a name="consuming-the-proxy"></a>使用代理

生成的代理类提供用于使用 web 服务使用异步编程模型 (APM) 设计模式的方法。 在此模式下，异步操作作为名为*BeginOperationName*和*EndOperationName*的两个方法实现，该方法用于开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回实现 `IAsyncResult` 接口的对象。 调用*BeginOperationName*之后，应用程序可以继续在调用线程上执行指令，同时异步操作在线程池线程上发生。

对于每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取操作的结果。 *EndOperationName*的返回值与同步 web 服务方法返回的类型相同。 下面的代码示例演示了此示例：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

任务并行库（TPL）可以通过将异步操作封装在同一 `Task` 对象中来简化使用 APM begin/end 方法对的过程。 此封装由 `Task.Factory.FromAsync` 方法的多个重载提供。 此方法创建一个 `Task`，该在 `TodoServiceClient.BeginGetTodoItems` 方法完成后执行 `TodoServiceClient.EndGetTodoItems` 方法，并使用 `null` 参数指示没有数据传入 `BeginGetTodoItems` 委托。 最后，`TaskCreationOptions` 枚举的值指定应使用任务的创建和执行的默认行为。

有关 APM 的详细信息，请参阅 MSDN 上的[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和传统 .NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)。

有关使用 WCF 服务的详细信息，请参阅[使用 Windows Communication Foundation （WCF） Web 服务](~/xamarin-forms/data-cloud/web-services/wcf.md)。

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>使用传输安全

WCF 服务可以使用传输级安全性来防止消息被拦截。 Xamarin 平台支持使用 SSL 使用传输级安全的绑定。 但是，在某些情况下，堆栈可能需要验证证书，这会导致意外的行为。 可以通过在调用服务之前注册 `ServerCertificateValidationCallback` 委托来重写验证，如以下代码示例所示：

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

这会在忽略服务器端证书验证的同时，维护传输加密。 但是，这种方法有效地忽略与证书关联的信任问题，可能不适合。 有关详细信息，请参阅在[mono-project.com](https://www.mono-project.com)中[使用受信任的根 Respectfully](https://www.mono-project.com/UsingTrustedRootsRespectfully) 。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>使用客户端凭据安全

WCF 服务还可能要求服务客户端使用凭据进行身份验证。 Xamarin 平台不支持 WS 安全协议，该协议允许客户端在 SOAP 消息信封中发送凭据。 但是，Xamarin 平台支持通过指定适当的 `ClientCredentialType`将 HTTP 基本身份验证凭据发送到服务器的功能：

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

然后，可以指定基本身份验证凭据：

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

在上述示例中，如果您收到消息 "trampolines of 类型 0"，则可以通过将 `–aot “trampolines={number of trampolines}”` 参数添加到生成中来增加类型 0 trampolines 的数目。 有关详细信息，请参阅 [故障排除](~/ios/troubleshooting/troubleshooting.md#trampolines)。

有关 HTTP 基本身份验证的详细信息，请参阅对[RESTful Web 服务进行身份验证](~/xamarin-forms/data-cloud/authentication/rest.md)。

## <a name="related-links"></a>相关链接

- [Xamarin 中的 Web 服务](~/xamarin-forms/data-cloud/index.yml)
- [Svcutil.exe 元数据实用工具（）](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
