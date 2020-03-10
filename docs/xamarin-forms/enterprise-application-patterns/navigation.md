---
title: 企业应用导航
description: 本章介绍了 eShopOnContainers 移动应用如何从视图模型执行视图模型优先导航。
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0f523c7149366cff85164f26f3f47b87801002cb
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916476"
---
# <a name="enterprise-app-navigation"></a>企业应用导航

Xamarin 包含对页面导航的支持，这通常是由于用户与 UI 交互引起的，或者由于内部逻辑驱动状态更改而导致的应用本身。 但是，导航可能会很复杂，无法在使用模型-视图-ViewModel （MVVM）模式的应用程序中实现，因为必须满足以下挑战：

- 如何识别要导航到的视图，方法是使用不会在视图之间引入紧密耦合和依赖项的方法。
- 如何协调和初始化要导航到的视图的进程。 使用 MVVM 时，需要通过视图的绑定上下文实例化视图和视图模型，并使彼此关联。 当应用使用依赖关系注入容器时，视图模型和视图模型的实例化可能需要特定构造机制。
- 是执行视图优先导航，还是查看模型优先导航。 通过视图优先导航，要导航到的页面指的是视图类型的名称。 在导航过程中，将实例化指定的视图及其相应的视图模型和其他相关服务。 另一种方法是使用 "查看模型优先" 导航，其中要导航到的页面指的是视图模型类型的名称。
- 如何在视图和视图模型之间完全分离应用程序的导航行为。 MVVM 模式提供应用程序的 UI 及其表示形式和业务逻辑之间的隔离。 但是，应用的导航行为通常会跨越应用程序的 UI 和演示部分。 用户通常会从视图中启动导航，并将该视图替换为导航结果。 但是，导航通常还需要在视图模型中启动或协调。
- 如何在导航期间为初始化目的传递参数。 例如，如果用户导航到某一视图以更新订单详细信息，则必须将订单数据传递到该视图，以便它能够显示正确的数据。
- 如何进行坐标导航，以确保某些业务规则是服从的。 例如，可能会在离开视图之前提示用户，以使用户能够更正任何无效的数据，或提示用户提交或放弃在视图中进行的任何数据更改。

本章介绍了用于执行视图模型-首页导航的 `NavigationService` 类，从而解决了这些难题。

> [!NOTE]
> 应用使用的 `NavigationService` 仅用于在 ContentPage 实例之间执行层次结构导航。 使用服务在其他页类型之间导航可能会导致意外的行为。

## <a name="navigating-between-pages"></a>在页面之间导航

导航逻辑可以驻留在视图的代码隐藏中，也可以位于数据绑定视图模型中。 虽然在视图中放置导航逻辑可能是最简单的方法，但不能通过单元测试轻松地对其进行测试。 在视图模型类中放置导航逻辑意味着可以通过单元测试执行逻辑。 此外，视图模型接下来即可实现用于控制导航的逻辑，以确保强制实施特定业务规则。 例如，应用可能不会允许用户离开页面，而无需首先确保输入的数据是有效的。

通常会从视图模型调用 `NavigationService` 类，以提高可测试性。 但是，导航到视图模型中的视图将需要视图模型引用视图，特别是活动视图模型与之不关联的视图（不建议这样做）。 因此，此处显示的 `NavigationService` 将视图模型类型指定为要导航到的目标。

EShopOnContainers 移动应用使用 `NavigationService` 类提供视图模型优先导航。 此类实现 `INavigationService` 接口，如下面的代码示例所示：

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

此接口指定实现类必须提供以下方法：

|方法|目的|
|--- |--- |
|`InitializeAsync`|启动应用程序时，执行到两个页面之一的导航。|
|`NavigateToAsync`|执行到指定页的层次结构导航。|
|`NavigateToAsync(parameter)`|执行到指定页的分层导航，传递参数。|
|`RemoveLastFromBackStackAsync`|从导航堆栈中移除上一页。|
|`RemoveBackStackAsync`|从导航堆栈中移除所有以前的页面。|

此外，`INavigationService` 接口指定实现类必须提供 `PreviousPageViewModel` 属性。 此属性返回与导航堆栈中的上一页关联的视图模型类型。

> [!NOTE]
> `INavigationService` 接口通常还会指定 `GoBackAsync` 方法，该方法用于以编程方式返回到导航堆栈中的上一页。 但是，eShopOnContainers 移动应用中缺少此方法，因为这不是必需的。

### <a name="creating-the-navigationservice-instance"></a>创建 System.windows.navigation.navigationservice> 实例

实现 `INavigationService` 接口的 `NavigationService` 类将注册为具有 Autofac 依赖关系注入容器的单一实例，如以下代码示例所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

在 `ViewModelBase` 类构造函数中解析 `INavigationService` 接口，如下面的代码示例所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

这会返回对存储在 Autofac 依赖关系注入容器中的 `NavigationService` 对象的引用，该容器由 `App` 类中的 `InitNavigation` 方法创建。 有关详细信息，请参阅在[应用启动时进行导航](#navigating_when_the_app_is_launched)。

`ViewModelBase` 类将 `NavigationService` 实例存储在类型为 `INavigationService`的 `NavigationService` 属性中。 因此，派生自 `ViewModelBase` 类的所有视图模型类都可以使用 `NavigationService` 属性来访问 `INavigationService` 接口指定的方法。 这样可以避免将 `NavigationService` 对象从 Autofac 依赖关系注入容器注入到每个视图模型类中的开销。

### <a name="handling-navigation-requests"></a>处理导航请求

Xamarin 提供了[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)类，该类可实现分层导航体验，用户可以根据需要在页面间导航。 有关分层导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

EShopOnContainers 应用程序不直接使用[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)类，而是包装 `CustomNavigationView` 类中的 `NavigationPage` 类，如下面的代码示例所示：

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

此包装的目的是为了方便地设置类的 XAML 文件中的[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)实例的样式。

在视图模型类内执行导航，方法是调用 `NavigateToAsync` 方法之一，指定要导航到的页面的视图模型类型，如下面的代码示例所示：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下面的代码示例演示了 `NavigationService` 类提供的 `NavigateToAsync` 方法：

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

每个方法都允许派生自 `ViewModelBase` 类的任何视图模型类通过调用 `InternalNavigateToAsync` 方法来执行分层导航。 此外，第二个 `NavigateToAsync` 方法启用导航数据，将其指定为传递到要导航到的视图模型的参数，该参数通常用于执行初始化。 有关详细信息，请参阅[在导航过程中传递参数](#passing_parameters_during_navigation)。

`InternalNavigateToAsync` 方法执行导航请求，如以下代码示例所示：

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

`InternalNavigateToAsync` 方法通过首先调用 `CreatePage` 方法来执行到视图模型的导航。 此方法查找对应于指定视图模型类型的视图，并创建并返回此视图类型的实例。 查找与视图模型类型对应的视图时，将使用基于约定的方法，该方法假定：

- 视图与视图模型类型位于同一程序集中。
- 视图在中。查看子命名空间。
- 视图模型在中。Viewmodel 子命名空间。
- 视图名称对应于视图模型名称，并且已删除 "模型"。

实例化视图时，该视图与对应的视图模型关联。 有关这种情况的详细信息，请参阅[自动创建具有视图模型定位器的视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

如果正在创建的视图是 `LoginView`，则会将其包装在 `CustomNavigationView` 类的新实例中，并将其分配给[`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage)属性。 否则，将检索 `CustomNavigationView` 实例，并且如果该实例不为 null，则调用[`PushAsync`](xref:Xamarin.Forms.NavigationPage)方法将正在创建的视图推送到导航堆栈上。 但是，如果 `null`检索到的 `CustomNavigationView` 实例，则将在 `CustomNavigationView` 类的新实例中包装正在创建的视图并将其分配给 `Application.Current.MainPage` 属性。 此机制可确保在导航过程中，当页为空时以及包含数据时，会将页正确地添加到导航堆栈中。

> [!TIP]
> 请考虑缓存页面。 页缓存会导致当前未显示的视图占用内存。 但是，如果不使用页缓存，则意味着在每次导航到新页时都会发生 XAML 分析和页的视图模型的构造，这会对复杂页造成性能影响。 对于不使用过多控件的设计良好的页面，性能应该足以满足需要。 但是，如果遇到慢速页面加载时间，页缓存可能会有所帮助。

创建并导航到视图后，将执行视图的关联视图模型的 `InitializeAsync` 方法。 有关详细信息，请参阅[在导航过程中传递参数](#passing_parameters_during_navigation)。

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>在应用启动时进行导航

启动应用时，将调用 `App` 类中的 `InitNavigation` 方法。 下面的代码示例演示此方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

方法在 Autofac 依赖关系注入容器中创建一个新的 `NavigationService` 对象，并在调用其 `InitializeAsync` 方法之前，返回对该对象的引用。

> [!NOTE]
> 当 `ViewModelBase` 类解析 `INavigationService` 接口时，容器将返回对在调用 InitNavigation 方法时创建的 `NavigationService` 对象的引用。

下面的代码示例演示了 `NavigationService` `InitializeAsync` 方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

如果应用具有缓存的访问令牌（用于身份验证），则导航到 `MainView`。 否则，将导航到 `LoginView`。

有关 Autofac 依赖关系注入容器的详细信息，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>在导航过程中传递参数

由 `INavigationService` 接口指定的 `NavigateToAsync` 方法之一允许将导航数据指定为传递到要导航到的视图模型的参数，该参数通常用于执行初始化。

例如，`ProfileViewModel` 类包含当用户在 `ProfileView` 页上选择订单时执行的 `OrderDetailCommand`。 接下来，这会执行 `OrderDetailAsync` 方法，如以下代码示例所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

此方法调用 `OrderDetailViewModel`的导航，同时传递表示用户在 `ProfileView` 页上选择的顺序的 `Order` 实例。 当 `NavigationService` 类创建 `OrderDetailView`时，将实例化 `OrderDetailViewModel` 类并将其分配给视图的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)。 导航到 `OrderDetailView`后，`InternalNavigateToAsync` 方法执行视图的关联视图模型的 `InitializeAsync` 方法。

`InitializeAsync` 方法在 `ViewModelBase` 类中定义为可重写的方法。 此方法指定一个 `object` 参数，该参数表示导航操作期间要传递到视图模型的数据。 因此，要从导航操作接收数据的视图模型类提供自己的 `InitializeAsync` 方法实现来执行所需的初始化。 下面的代码示例演示了 `OrderDetailViewModel` 类中的 `InitializeAsync` 方法：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

此方法检索在导航操作期间传递到视图模型中的 `Order` 实例，并使用它来检索 `OrderService` 实例的完整订单详细信息。

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>使用行为调用导航

导航通常由用户交互从视图触发。 例如，`LoginView` 在身份验证成功后执行导航。 下面的代码示例演示如何通过行为调用导航：

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

在运行时，`EventToCommandBehavior` 将响应与[`WebView`](xref:Xamarin.Forms.WebView)的交互。 当 `WebView` 导航到网页时，将激发[`Navigating`](xref:Xamarin.Forms.WebView.Navigating)事件，这将在 `LoginViewModel`中执行 `NavigateCommand`。 默认情况下，事件的事件参数将传递给命令。 此数据将在源和目标之间通过 `EventArgsConverter` 属性中指定的转换器进行转换，后者从[`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs)返回[`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) 。 因此，当执行 `NavigationCommand` 时，网页的 Url 作为参数传递给注册的 `Action`。

接下来，`NavigationCommand` 执行 `NavigateAsync` 方法，如以下代码示例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

此方法调用 `MainViewModel`的导航，并在导航后从导航堆栈中移除 `LoginView` 页面。

### <a name="confirming-or-cancelling-navigation"></a>确认或取消导航

在导航操作期间，应用可能需要与用户交互，以便用户可以确认或取消导航。 例如，当用户尝试在完全完成数据输入页之前进行导航时，这可能是必需的。 在这种情况下，应用程序应提供通知，以允许用户离开页面，或在发生之前取消导航操作。 这可以通过使用来自通知的响应来控制是否调用导航来实现视图模型类。

## <a name="summary"></a>总结

Xamarin.Forms 包含对页面导航的支持，通常在逻辑驱动的状态更改时，因用户与 UI 交互或通过应用本身而引起页面导航。 但是，在使用 MVVM 模式的应用中实现导航可能较为复杂。

本章介绍了一个 `NavigationService` 类，该类用于执行视图模型的视图模型优先导航。 将导航逻辑放置在视图模型类中意味着可通过自动测试来运用该逻辑。 此外，视图模型接下来即可实现用于控制导航的逻辑，以确保强制实施特定业务规则。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
