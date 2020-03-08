---
title: 依赖关系注入
description: 本章介绍了 eShopOnContainers 移动应用如何使用依赖关系注入将具体类型从依赖于这些类型的代码中分离出来。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 08fb22627ab6b40c94c17d94321ed0bac60beedd
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915324"
---
# <a name="dependency-injection"></a>依赖关系注入

通常，在实例化对象时将调用类构造函数，并且该对象需要的任何值都将作为参数传递到构造函数。 这是依赖关系注入的示例，专门称为*构造函数注入*。 将对象所需的依赖项注入构造函数。

通过将依赖项指定为接口类型，依赖关系注入允许从依赖于这些类型的代码分离具体类型。 它通常使用容器来保存接口与抽象类型之间的注册和映射列表，以及实现或扩展这些类型的具体类型。

还有其他类型的依赖项注入，如*属性 setter 注入*和*方法调用注入*，但不太常见。 因此，本章重点介绍如何通过依赖关系注入容器来执行构造函数注入。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>依赖关系注入简介

依赖关系注入是一种专用的控制反转（IoC）模式，在这种情况下，要反转的问题是获取所需依赖关系的过程。 使用依赖关系注入，另一个类负责在运行时将依赖项注入到对象。 下面的代码示例演示如何在使用依赖关系注入时构造 `ProfileViewModel` 类：

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

`ProfileViewModel` 构造函数将 `IOrderService` 实例作为自变量接收，由其他类插入。 `ProfileViewModel` 类中的唯一依赖项在接口类型上。 因此，`ProfileViewModel` 类并不知道负责实例化 `IOrderService` 对象的类。 类，该类负责实例化 `IOrderService` 对象，并将其插入到 `ProfileViewModel` 类中，称为*依赖关系注入容器*。

依赖关系注入根据容器的配置提供用于实例化类实例和管理器生命周期的工具，通过此方法减少对象之间的耦合。 在对象创建期间，容器将向对象中注入其所需的任何依赖关系。 如果这些依赖关系尚未创建，容器将首先创建并解析其依赖关系。

> [!NOTE]
> 还可以使用工厂手动实现依赖关系注入。 但是，使用容器可提供其他功能（例如生存期管理），并通过程序集扫描进行注册。

使用依赖关系注入容器有多个优点：

- 容器不再需要类来查找依赖项并管理其生存期。
- 容器允许映射已实现的依赖项，而不影响类。
- 通过允许模拟的依赖项，容器有助于实现可测试性。
- 容器通过允许将新类轻松添加到应用中来提高可维护性。

在使用 MVVM 的 Xamarin 窗体应用的上下文中，依赖关系注入容器通常用于注册和解析视图模型，并用于注册服务并将其注入查看模型中。

有许多依赖关系注入容器可用，eShopOnContainers 移动应用使用 TinyIoC 管理应用中的视图模型和服务类的实例化。 TinyIoC 是在评估多个不同的容器后选择的，并且在与大多数常见容器进行比较时，它们的性能优于移动平台。 它有助于构建松散耦合的应用程序，并提供了依赖关系注入容器中常见的所有功能，包括用于注册类型映射、解析对象、管理对象生存期以及将依赖对象注入到其中的方法它解析的对象的构造函数。 有关 TinyIoC 的详细信息，请参阅 github.com 上的[TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) 。

在 TinyIoC 中，`TinyIoCContainer` 类型提供了依赖关系注入容器。 图3-1 显示了使用此容器时的依赖项，这将实例化 `IOrderService` 对象并将其插入 `ProfileViewModel` 类中。

![](dependency-injection-images/dependencyinjection.png "Dependencies example when using dependency injection")

**图3-1：** 使用依赖关系注入时的依赖关系

在运行时，容器必须知道它应实例化 `IOrderService` 接口的哪一实现，然后才能实例化 `ProfileViewModel` 对象。 这涉及：

- 用于决定如何实例化实现 `IOrderService` 接口的对象的容器。 这称为 "*注册*"。
- 用于实例化实现 `IOrderService` 接口和 `ProfileViewModel` 对象的对象的容器。 这称为 "*解析*"。

最终，应用程序将使用 `ProfileViewModel` 对象完成，并可用于垃圾回收。 此时，如果其他类不共享同一个实例，垃圾回收器应释放 `IOrderService` 实例。

> [!TIP]
> 编写与容器无关的代码。 请始终尝试编写与容器无关的代码，以便将应用与所使用的特定依赖关系容器分离。

## <a name="registration"></a>注册

在将依赖项注入到对象之前，必须先使用容器注册依赖项的类型。 注册类型通常涉及传递容器接口和实现接口的具体类型。

通过代码在容器中注册类型和对象有两种方法：

- 使用容器注册类型或映射。 如果需要，容器将生成指定类型的实例。
- 将容器中的现有对象注册为单一实例。 如果需要，容器将返回对现有对象的引用。

> [!TIP]
> 依赖关系注入容器并非始终适合。 依赖关系注入会引入额外的复杂性和要求，这些要求可能不适用于小型应用程序。 如果某个类没有任何依赖项，或者它不是其他类型的依赖项，则将其放在容器中可能并不合理。 此外，如果类具有一组不属于类型的整数依赖项，并且永远不会发生更改，则将其放在容器中可能没有意义。

需要依赖关系注入的类型的注册应在应用的单个方法中执行，此方法应在应用生命周期中及早调用，以确保应用知道其类之间的依赖关系。 在 eShopOnContainers 移动应用中，这是由 `ViewModelLocator` 类执行的，该类生成 `TinyIoCContainer` 对象，是应用中的唯一包含对该对象的引用的类。 下面的代码示例演示了 eShopOnContainers 移动应用如何声明 `ViewModelLocator` 类中的 `TinyIoCContainer` 对象：

```csharp
private static TinyIoCContainer _container;
```

类型是在 `ViewModelLocator` 构造函数中注册的。 这是通过首先创建 `TinyIoCContainer` 实例实现的，如以下代码示例所示：

```csharp
_container = new TinyIoCContainer();
```

然后，将用 `TinyIoCContainer` 对象注册类型，下面的代码示例演示类型注册的最常见形式：

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

此处所示的 `Register` 方法将接口类型映射到具体类型。 默认情况下，每个接口注册都配置为单一实例，以便每个依赖对象都接收相同的共享实例。 因此，容器中将只存在一个 `RequestProvider` 实例，该容器由需要通过构造函数注入 `IRequestProvider` 的对象共享。

也可以直接注册具体类型，无需从接口类型进行映射，如下面的代码示例所示：

```csharp
_container.Register<ProfileViewModel>();
```

默认情况下，每个具体的类注册都配置为多实例，以便每个依赖对象收到一个新实例。 因此，解析 `ProfileViewModel` 时，将创建一个新实例，并且容器将注入其所需的依赖项。

<a name="resolution" />

## <a name="resolution"></a>解决方法

类型注册后，可以将其解析或注入为依赖项。 当正在解析类型并且容器需要创建新的实例时，它会将任何依赖关系注入到该实例中。

通常，解析类型时，会出现以下三种情况之一：

1. 如果该类型尚未注册，则容器会引发异常。
1. 如果该类型已注册为单一实例，则容器返回单一实例。 如果这是第一次调用该类型的，则容器会根据需要创建它，并保持对它的引用。
1. 如果该类型尚未注册为单一实例，则容器返回一个新的实例，并且不保留对该实例的引用。

下面的代码示例演示了如何解决之前向 TinyIoC 注册的 `RequestProvider` 类型：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此示例中，要求 TinyIoC 解析 `IRequestProvider` 类型和任何依赖项的具体类型。 通常，当需要特定类型的实例时，将调用 `Resolve` 方法。 有关控制已解析对象的生存期的信息，请参阅[管理已解析对象的生存期](#managing_the_lifetime_of_resolved_objects)。

下面的代码示例演示了 eShopOnContainers 移动应用如何实例化视图模型类型及其依赖项：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此示例中，要求 TinyIoC 解析请求的视图模型的视图模型类型，并且该容器还将解析任何依赖项。 解析 `ProfileViewModel` 类型时，要解析的依赖项是一个 `ISettingsService` 对象和一个 `IOrderService` 对象。 由于在注册 `SettingsService` 和 `OrderService` 类时使用了接口注册，因此 TinyIoC 返回 `SettingsService` 和 `OrderService` 类的单独实例，然后将它们传递给 `ProfileViewModel` 类的构造函数。 有关 eShopOnContainers mobile 应用如何构造视图模型并将其与视图相关联的详细信息，请参阅[自动创建具有视图模型定位器的视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

> [!NOTE]
> 使用容器来注册和解析类型会影响性能，因为容器使用反射来创建每个类型，特别是在应用中为每个页面导航重构依赖关系的情况。 如果存在许多或深度依赖关系，则创建成本会显著增加。

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解析对象的生存期

使用具体的类注册注册类型后，TinyIoC 的默认行为是在每次解析类型时或者在依赖项机制将实例注入到其他类时，创建已注册类型的新实例。 在这种情况下，容器不会保存对已解析的对象的引用。 但是，在使用接口注册注册类型时，TinyIoC 的默认行为是将该对象的生存期作为单一实例进行管理。 因此，当容器处于范围内时，实例将保留在范围中，当容器超出范围并进行垃圾回收时，或者当代码显式释放容器时，实例将被释放。

可以使用熟知的 `AsSingleton` 和 `AsMultiInstance` API 方法来重写默认的 TinyIoC 注册行为。 例如，`AsSingleton` 方法可与 `Register` 方法一起使用，以便容器在调用 `Resolve` 方法时创建或返回类型的单一实例。 下面的代码示例显示了如何指示 TinyIoC 创建 `LoginViewModel` 类的单一实例：

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

第一次解析 `LoginViewModel` 类型时，容器将创建新的 `LoginViewModel` 对象并维护对它的引用。 在 `LoginViewModel`的任何后续解决方案中，容器将返回对之前创建的 `LoginViewModel` 对象的引用。

> [!NOTE]
> 注册为单一实例的类型会在容器被释放时释放。

## <a name="summary"></a>总结

依赖关系注入将启用通过依赖于具体类型的代码来分离这些类型。 它通常使用一个容器，该容器包含一系列注册和接口与抽象类型间的映射以及实现或扩展这些类型的具体类型。

TinyIoC 是一种轻型容器，与大多数常见容器相比，它在移动平台上具有优异的性能。 它有助于构建松散耦合的应用程序，并提供了依赖关系注入容器中常见的所有功能，包括用于注册类型映射、解析对象、管理对象生存期以及将依赖对象注入到其中的方法它解析的对象的构造函数。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
