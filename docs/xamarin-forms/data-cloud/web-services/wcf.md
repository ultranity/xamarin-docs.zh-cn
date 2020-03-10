---
title: 使用 Windows Communication Foundation （WCF） Web 服务
description: 本文演示如何使用 Xamarin.Forms 应用程序中的 WCF 简单对象访问协议 (SOAP) 服务。
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 28cb1573262b63cc2b0ccad9f468fe36c682718d
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915354"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>使用 Windows Communication Foundation （WCF） Web 服务

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF 是 Microsoft 的统一框架，用于生成面向服务的应用程序。它使开发人员能够构建安全、可靠、可互操作的分布式应用程序。本文演示如何从 Xamarin 应用程序使用 WCF 简单对象访问协议（SOAP）服务。_

WCF 描述了具有各种不同协定的服务，包括：

- **数据协定**–定义构成消息中内容的基础的数据结构。
- **消息协定**–基于现有数据协定撰写消息。
- **故障协定**–允许指定自定义 SOAP 错误。
- **服务协定**–指定服务支持的操作，以及与每个操作交互所需的消息。 它们还指定可以与每个服务上的操作相关联的任何自定义错误行为。

ASP.NET Web Services （.ASMX）和 WCF 之间存在差异，但 WCF 支持与通过 HTTP 提供的 SOAP 消息相同的功能。 有关使用 .ASMX 服务的详细信息，请参阅[使用 ASP.NET Web 服务（.asmx）](~/xamarin-forms/data-cloud/web-services/asmx.md)。

> [!IMPORTANT]
> 使用 `BasicHttpBinding` 类通过 HTTP/HTTPS 将 Xamarin 平台支持限定为经过文本编码的 SOAP 消息。
>
> WCF 支持需要使用仅在 Windows 环境中可用的工具来生成代理并托管 TodoWCFService。 生成和测试 iOS 应用需要在 Windows 计算机上部署 TodoWCFService，或将其部署为 Azure web 服务。
>
> Xamarin 窗体本机应用通常与 .NET Standard 类库共享代码。 但是，.NET Core 当前不支持 WCF，因此共享项目必须是旧的可移植类库。 有关 .NET Core 中 WCF 支持的信息，请参阅[为服务器应用选择 .Net core 和 .NET Framework](/dotnet/standard/choosing-core-framework-server)。

示例应用程序解决方案包括一个可在本地运行的 WCF 服务，如以下屏幕截图所示：

![](wcf-images/portal.png "Sample Application")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
>
> 如果无法为 internet 资源使用 `HTTPS` 协议和安全通信，则可以选择不使用 ATS。 这可以通过更新应用的**info.plist**文件来实现。 有关详细信息，请参阅[应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>使用 Web 服务

WCF 服务提供了以下操作：

|Operation|说明|parameters|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关应用程序中使用的数据模型的详细信息，请参阅[对数据进行建模](~/xamarin-forms/data-cloud/web-services/introduction.md)。

必须生成*代理*以使用 WCF 服务，该服务允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 此元数据中生成的 web 服务的 Web 服务描述语言 (WSDL) 文档的形式公开。 在 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider，若要将 web 服务的服务引用添加到.NET Standard 库，可以生成代理。 创建的代理帐户在 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider 的替代方法是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 有关详细信息，请参阅 " [svcutil.exe" 元数据实用工具（）](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/)。

生成的代理类提供用于使用 web 服务使用异步编程模型 (APM) 设计模式的方法。 在此模式下，异步操作作为名为*BeginOperationName*和*EndOperationName*的两个方法实现，该方法用于开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回实现 `IAsyncResult` 接口的对象。 调用*BeginOperationName*之后，应用程序可以继续在调用线程上执行指令，同时异步操作在线程池线程上发生。

对于每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取操作的结果。 *EndOperationName*的返回值与同步 web 服务方法返回的类型相同。 例如，`EndGetTodoItems` 方法返回 `TodoItem` 实例的集合。 *EndOperationName*方法还包括一个 `IAsyncResult` 参数，该参数应设置为由对*BeginOperationName*方法的相应调用返回的实例。

任务并行库（TPL）可以通过将异步操作封装在同一 `Task` 对象中来简化使用 APM begin/end 方法对的过程。 此封装由 `TaskFactory.FromAsync` 方法的多个重载提供。

有关 APM 的详细信息，请参阅 MSDN 上的[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和传统 .NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)。

### <a name="create-the-todoserviceclient-object"></a>创建 TodoServiceClient 对象

生成的代理类提供 `TodoServiceClient` 类，该类用于通过 HTTP 与 WCF 服务进行通信。 它提供用于调用 web 服务方法从 URI 的异步操作来标识服务实例的功能。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`TodoServiceClient` 实例在类级声明，以便在应用程序需要使用 WCF 服务时，该对象的生存时间，如以下代码示例所示：

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

`TodoServiceClient` 实例配置了绑定信息和终结点地址。 使用绑定指定传输、 编码和协议详细信息所需的应用程序和服务彼此通信。 `BasicHttpBinding` 指定将通过 HTTP 传输协议发送文本编码的 SOAP 消息。 指定终结点地址可以连接到 WCF 服务的不同实例应用程序，前提是有多个已发布的实例。

有关配置服务引用的详细信息，请参阅[配置服务引用](~/cross-platform/data-cloud/web-services/index.md#wcf)。

### <a name="create-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用 `TodoItem` 类来为数据建模。 若要在 web 服务中存储 `TodoItem` 项，必须先将它转换为生成 `TodoItem` 类型的代理。 这是由 `ToWCFServiceTodoItem` 方法完成的，如下面的代码示例所示：

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

此方法只是创建一个新的 `TodoWCFService.TodoItem` 实例，并将每个属性设置为 `TodoItem` 实例中的相同属性。

同样，从 web 服务检索数据时，必须将其从 `TodoItem` 类型生成的代理转换为 `TodoItem` 实例。 这是通过 `FromWCFServiceTodoItem` 方法来完成的，如下面的代码示例所示：

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

此方法只是从 `TodoItem` 类型生成的代理中检索数据，并在新创建的 `TodoItem` 实例中设置数据。

### <a name="retrieve-data"></a>检索数据

`TodoServiceClient.BeginGetTodoItems` 和 `TodoServiceClient.EndGetTodoItems` 方法用于调用 web 服务提供的 `GetTodoItems` 操作。 这些异步方法封装在 `Task` 对象中，如下面的代码示例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync` 方法创建一个 `Task`，它在 `TodoServiceClient.BeginGetTodoItems` 方法完成后执行 `TodoServiceClient.EndGetTodoItems` 方法，并使用 `null` 参数指示没有数据传入 `BeginGetTodoItems` 委托。 最后，`TaskCreationOptions` 枚举的值指定应使用任务的创建和执行的默认行为。

`TodoServiceClient.EndGetTodoItems` 方法返回 `TodoWCFService.TodoItem` 实例的 `ObservableCollection`，然后将其转换为 `TodoItem` 实例的 `List` 以供显示。

### <a name="create-data"></a>创建数据

`TodoServiceClient.BeginCreateTodoItem` 和 `TodoServiceClient.EndCreateTodoItem` 方法用于调用 web 服务提供的 `CreateTodoItem` 操作。 这些异步方法封装在 `Task` 对象中，如下面的代码示例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` 方法创建一个 `Task`，它在 `TodoServiceClient.BeginCreateTodoItem` 方法完成后执行 `TodoServiceClient.EndCreateTodoItem` 方法，其中 `todoItem` 参数是传递到 `BeginCreateTodoItem` 委托中的数据，用于指定 web 服务要创建的 `TodoItem`。 最后，`TaskCreationOptions` 枚举的值指定应使用任务的创建和执行的默认行为。

如果 web 服务无法创建 `TodoItem`（由应用程序处理），则会引发 `FaultException`。

### <a name="update-data"></a>更新数据

`TodoServiceClient.BeginEditTodoItem` 和 `TodoServiceClient.EndEditTodoItem` 方法用于调用 web 服务提供的 `EditTodoItem` 操作。 这些异步方法封装在 `Task` 对象中，如下面的代码示例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` 方法创建一个 `Task`，它在 `TodoServiceClient.BeginCreateTodoItem` 方法完成后执行 `TodoServiceClient.EndEditTodoItem` 方法，其中 `todoItem` 参数是传递到 `BeginEditTodoItem` 委托中的数据，用于指定 web 服务要更新的 `TodoItem`。 最后，`TaskCreationOptions` 枚举的值指定应使用任务的创建和执行的默认行为。

如果 web 服务无法找到或更新 `TodoItem`（由应用程序处理），则它会引发 `FaultException`。

### <a name="delete-data"></a>删除数据

`TodoServiceClient.BeginDeleteTodoItem` 和 `TodoServiceClient.EndDeleteTodoItem` 方法用于调用 web 服务提供的 `DeleteTodoItem` 操作。 这些异步方法封装在 `Task` 对象中，如下面的代码示例所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` 方法创建一个 `Task`，它在 `TodoServiceClient.BeginDeleteTodoItem` 方法完成后执行 `TodoServiceClient.EndDeleteTodoItem` 方法，其中 `id` 参数是传递到 `BeginDeleteTodoItem` 委托中的数据，用于指定 web 服务要删除的 `TodoItem`。 最后，`TaskCreationOptions` 枚举的值指定应使用任务的创建和执行的默认行为。

如果 web 服务找不到或无法删除 `TodoItem`（由应用程序处理），则会引发 `FaultException`。

## <a name="configure-remote-access-to-iis-express"></a>配置对 IIS Express 的远程访问
在 Visual Studio 2017 或 Visual Studio 2019 中，你应该能够在没有其他配置的情况下在电脑上测试 UWP 应用程序。 测试 Android 和 iOS 客户端可能需要此部分中的其他步骤。 有关详细信息，请参阅[从 IOS 模拟器和 Android 模拟器连接到本地 Web 服务](~/cross-platform/deploy-test/connect-to-local-web-services.md)。

默认情况下，IIS Express 仅响应 `localhost`的请求。 远程设备（如 Android 设备，iPhone 甚至是模拟器）将无法访问本地 WCF 服务。 需要在本地网络上了解 Windows 10 工作站的 IP 地址。 出于本示例的目的，假设工作站具有 `192.168.1.143`的 IP 地址。 以下步骤说明如何配置 Windows 10 和 IIS Express 以接受远程连接并从物理或虚拟设备连接到服务：

1. **向 Windows 防火墙添加例外**。 必须通过 Windows 防火墙打开端口，子网中的应用程序才能使用这些应用程序与 WCF 服务进行通信。 在防火墙中创建入站规则打开端口49393。 在管理命令提示符下，运行以下命令：

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **将 IIS Express 配置为接受远程连接**。 可以通过在 **[解决方案目录]\.vs\config\applicationhost.config**中编辑 IIS Express 的配置文件来配置 IIS Express。查找名称为 `TodoWCFService`的 `site` 元素。 它应类似于以下 XML：

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    需要添加两个 `binding` 元素，以打开端口49393到外部流量和 Android 模拟器。 绑定使用 `[IP address]:[port]:[hostname]` 格式，该格式指定 IIS Express 对请求的响应方式。 外部请求的主机名必须指定为 `binding`。 将以下 XML 添加到 `bindings` 元素，并将 IP 地址替换为你自己的 IP 地址：

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    更改后，`bindings` 元素应该如下所示：

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >默认情况下，出于安全原因，IIS Express 将不接受来自外部源的连接。 若要启用远程设备的连接，必须使用管理权限运行 IIS Express。 执行此操作的最简单方法是运行具有管理权限的 Visual Studio 2017。 这会在运行 TodoWCFService 时，通过管理权限启动 IIS Express。

    完成这些步骤后，你应该能够运行 TodoWCFService 并从子网中的其他设备进行连接。 可以通过运行应用程序并访问 `http://localhost:49393/TodoService.svc`来对此进行测试。 如果在访问该 URL 时收到错误**请求**错误，则 `bindings` 在 IIS Express 配置中可能不正确（请求 IIS Express，但被拒绝）。 如果遇到其他错误，可能是因为应用程序未运行或防火墙配置不正确。

    若要允许 IIS Express 继续运行和服务，请关闭 "项目属性" 中的 "**编辑并继续**" 选项 **> Web > 调试器**。

1. **自定义终结点设备用于访问服务的终结点**。 此步骤包括配置在物理或模拟设备上运行的客户端应用程序，以访问 WCF 服务。

    Android 仿真程序利用内部代理，阻止模拟器直接访问主机的 `localhost` 地址。 相反，模拟器上的地址 `10.0.2.2` 通过内部代理路由到主机上的 `localhost`。 这些代理的请求将 `127.0.0.1` 作为请求标头中的主机名，这就是在上述步骤中为此主机名创建 IIS Express 绑定的原因。

    IOS 模拟器在 Mac 生成主机上运行，即使使用的是[适用于 Windows 的远程 IOS 模拟器](~/tools/ios-simulator/index.md)也是如此。 来自模拟器的网络请求会将本地网络上的工作站 IP 作为主机名（在此示例中，它是 `192.168.1.143`的，但实际的 IP 地址可能会不同）。 这就是在上述步骤中为此主机名创建 IIS Express 绑定的原因。

    确保 TodoWCF （可移植）项目中**Constants.cs**文件的 `SoapUrl` 属性的值对于你的网络是正确的：

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    使用适当的终结点配置**Constants.cs**后，应能够从物理或虚拟设备连接到 Windows 10 工作站上运行的 TodoWCFService。

## <a name="related-links"></a>相关链接

- [TodoWCF （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [如何：创建 Windows Communication Foundation 客户端](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Svcutil.exe 元数据实用工具（）](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
