---
ms.openlocfilehash: 90f3f9ff5ed29a1ae2c93e355fc15bc6550d78dd
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135129"
---
在本练习中，你将创建使用 `RestService` 类的用户界面，而该类将检索 [OpenWeatherMap](https://openweathermap.org/) Web API 的数据。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“解决方案资源管理器”的 WebServiceTutorial 项目中，双击 MainPage.xaml 将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`Grid`](xref:Xamarin.Forms.Grid) 中的 [`Entry`](xref:Xamarin.Forms.Entry)、[`Button`](xref:Xamarin.Forms.Button) 和一系列 [`Label`](xref:Xamarin.Forms.Label) 实例组成。 `Entry` 通过设置其 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性预先填充了“Seattle”。 `Button` 将其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为名为 `OnButtonClicked` 的事件处理程序，将在下一步中创建该处理程序。 一半 `Label` 实例显示静态文本，其余实例数据绑定到 `WeatherData` 属性。 运行时，使用数据绑定的 `Label` 实例将查看各自用于 `WeatherData` 对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性，这些属性用于数据绑定表达式。 有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 具有使用 `x:Name` 属性指定的名称。 该操作使代码隐藏文件能够使用分配给它的名称访问对象。

1. 在“解决方案资源管理器”的 WebServiceTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后，在“MainPage.xaml.cs”中，删除所有模板代码，替换为以下代码  ：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    在点击 [`Button`](xref:Xamarin.Forms.Button) 时执行的 `OnButtonClicked` 方法调用 `RestService.GetWeatherDataAsync` 方法来检索名称已输入 [`Entry`](xref:Xamarin.Forms.Entry) 中的城市的天气数据。 `GetWeatherDataAsync` 方法需要 `string` 参数，该参数表示调用的是由 `GenerateRequestUri` 方法生成的 Web API 的 URI。 该方法获取存储在 `OpenWeatherMapEndpoint` 常数中的终结点地址，并向其添加指定以下内容的查询参数：

    - 请求的天气数据的所属城市。
    - 返回天气数据的单位。
    - 个人的 API 密钥。

    检索请求的天气数据后，页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为 `WeatherData` 对象。 有关 `BindingContext` 属性的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)指南中的[具有绑定上下文的绑定](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context)。

    > [!IMPORTANT]
    > [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性通过可视化树继承。 已在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象上设置了该属性，所以 `ContentPage` 的子对象继承它的值，包括 [`Label`](xref:Xamarin.Forms.Label) 实例。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  。 点击 [`Button`](xref:Xamarin.Forms.Button) 检索西雅图的当前天气数据：

    [![iOS 和 Android 上西雅图天气数据的屏幕截图](../images/consume-web-service.png "西雅图天气数据")](../images/consume-web-service-large.png#lightbox "西雅图天气数据")

    > [!IMPORTANT]
    > 必须将个人 OpenWeatherMap API 密钥设置为 `Constants` 类中 `OpenWeatherMapAPIKey` 常数值。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 Solution Pad 的 WebServiceTutorial 项目中，双击 MainPage.xaml 将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`Grid`](xref:Xamarin.Forms.Grid) 中的 [`Entry`](xref:Xamarin.Forms.Entry)、[`Button`](xref:Xamarin.Forms.Button) 和一系列 [`Label`](xref:Xamarin.Forms.Label) 实例组成。 `Entry` 通过设置其 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性预先填充了“Seattle”。 `Button` 将其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为名为 `OnButtonClicked` 的事件处理程序，将在下一步中创建该处理程序。 一半 `Label` 实例显示静态文本，其余实例数据绑定到 `WeatherData` 属性。 运行时，使用数据绑定的 `Label` 实例将查看各自用于 `WeatherData` 对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性，这些属性用于数据绑定表达式。 有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 具有使用 `x:Name` 属性指定的名称。 该操作使代码隐藏文件能够使用分配给它的名称访问对象。

    要详细了解如何在 Xamarin.Forms 中使用基于 REST 的 Web 服务，请参阅[使用 RESTful Web 服务（指南）](~/xamarin-forms/data-cloud/web-services/rest.md)。

1. 在 Solution Pad 的 WebServiceTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后，在“MainPage.xaml.cs”中，删除所有模板代码，替换为以下代码  ：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    在点击 [`Button`](xref:Xamarin.Forms.Button) 时执行的 `OnButtonClicked` 方法调用 `RestService.GetWeatherDataAsync` 方法来检索名称已输入 [`Entry`](xref:Xamarin.Forms.Entry) 中的城市的天气数据。 `GetWeatherDataAsync` 方法需要 `string` 参数，该参数表示调用的是由 `GenerateRequestUri` 方法生成的 Web API 的 URI。 该方法获取存储在 `OpenWeatherMapEndpoint` 常数中的终结点地址，并向其添加指定以下内容的查询参数：

    - 请求的天气数据的所属城市。
    - 返回天气数据的单位。
    - 个人的 API 密钥。

    检索请求的天气数据后，页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为 `WeatherData` 对象。 有关 `BindingContext` 属性的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)指南中的[具有绑定上下文的绑定](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context)。

    > [!IMPORTANT]
    > [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性通过可视化树继承。 已在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象上设置了该属性，所以 `ContentPage` 的子对象继承它的值，包括 [`Label`](xref:Xamarin.Forms.Label) 实例。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序  。 点击 [`Button`](xref:Xamarin.Forms.Button) 检索西雅图的当前天气数据：

    [![iOS 和 Android 上西雅图天气数据的屏幕截图](../images/consume-web-service.png "西雅图天气数据")](../images/consume-web-service-large.png#lightbox "西雅图天气数据")

    > [!IMPORTANT]
    > 必须将个人 OpenWeatherMap API 密钥设置为 `Constants` 类中 `OpenWeatherMapAPIKey` 常数值。

    要详细了解如何在 Xamarin.Forms 中使用基于 REST 的 Web 服务，请参阅[使用 RESTful Web 服务（指南）](~/xamarin-forms/data-cloud/web-services/rest.md)。
