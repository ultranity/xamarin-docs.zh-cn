---
ms.openlocfilehash: caee3eeda90a560f032c17657072ae5ba5023a69
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135126"
---
在本练习中，你将创建一个用户界面来使用以前创建的数据访问类。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“解决方案资源管理器”的 LocalDatabaseTutorial 项目中，双击 MainPage.xaml 将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由两个 [`Entry`](xref:Xamarin.Forms.Entry) 实例、一个 [`Button`](xref:Xamarin.Forms.Button) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的一个 [`ListView`](xref:Xamarin.Forms.ListView) 组成。 每个 `Entry` 都有其 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性集，用于指定在用户输入之前显示的占位符文本。 `Button` 将其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为名为 `OnButtonClicked` 的事件处理程序，将在下一步中创建该处理程序。 `ListView` 将其 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，后者使用 [`TextCell`](xref:Xamarin.Forms.TextCell) 来定义 [`ListView`](xref:Xamarin.Forms.ListView) 中每一行的外观。 `TextCell` 数据将其 [`Text`](xref:Xamarin.Forms.TextCell.Text) 和 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 属性分别绑定到每个 `Person` 对象的 `Name` 和 `Age` 属性。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 实例和 [`ListView`](xref:Xamarin.Forms.ListView) 具有使用 `x:Name` 属性指定的名称。 该操作使代码隐藏文件可以使用指定的名称访问这些对象。

1. 在“解决方案资源管理器”的 LocalDatabaseTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后在 MainPage.xaml.cs 中，将 `OnAppearing` 替代和 `OnButtonClicked` 事件处理程序添加到类  ：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    `OnAppearing` 方法使用存储在数据库中的任何数据填充 [`ListView`](xref:Xamarin.Forms.ListView)。 点击 [`Button`](xref:Xamarin.Forms.Button) 时执行的 `OnButtonClicked` 方法会在清除 [`Entry`](xref:Xamarin.Forms.Entry) 实例并刷新 `ListView` 中的数据之前将输入的数据保存到数据库中。

    > [!NOTE]
    > `OnAppearing` 方法覆盖的执行是在布局 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 后但在其变得可见之前进行的。 因此，这是设置 Xamarin.Forms 视图内容的好地方。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  。

    输入多项数据，为每项数据点击 [`Button`](xref:Xamarin.Forms.Button)。 这会将数据保存到数据库，并使用所有数据库数据重新填充 [`ListView`](xref:Xamarin.Forms.ListView)：

    [![iOS 和 Android 上的本地 SQLite.NET 数据库数据暂留的屏幕截图](../images/consume-data-access-classes.png "本地数据库数据暂留")](../images/consume-data-access-classes-large.png#lightbox "本地数据库数据暂留")

    有关 Xamarin.Forms 中本地数据库的详细信息，请参阅 [Xamarin.Forms 本地数据库（指南）](~/xamarin-forms/data-cloud/data/databases.md)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“Solution Pad”的 LocalDatabaseTutorial 项目中，双击 MainPage.xaml 将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由两个 [`Entry`](xref:Xamarin.Forms.Entry) 实例、一个 [`Button`](xref:Xamarin.Forms.Button) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的一个 [`ListView`](xref:Xamarin.Forms.ListView) 组成。 每个 `Entry` 都有其 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 属性集，用于指定在用户输入之前显示的占位符文本。 `Button` 将其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为名为 `OnButtonClicked` 的事件处理程序，将在下一步中创建该处理程序。 `ListView` 将其 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，后者使用 [`TextCell`](xref:Xamarin.Forms.TextCell) 来定义 [`ListView`](xref:Xamarin.Forms.ListView) 中每一行的外观。 `TextCell` 数据将其 [`Text`](xref:Xamarin.Forms.TextCell.Text) 和 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 属性分别绑定到每个 `Person` 对象的 `Name` 和 `Age` 属性。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 实例和 [`ListView`](xref:Xamarin.Forms.ListView) 具有使用 `x:Name` 属性指定的名称。 该操作使代码隐藏文件可以使用指定的名称访问这些对象。

1. 在“Solution Pad”的 LocalDatabaseTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开     。 然后在 MainPage.xaml.cs 中，将 `OnAppearing` 替代和 `OnButtonClicked` 事件处理程序添加到类  ：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    `OnAppearing` 方法使用存储在数据库中的任何数据填充 [`ListView`](xref:Xamarin.Forms.ListView)。 点击 [`Button`](xref:Xamarin.Forms.Button) 时执行的 `OnButtonClicked` 方法会在清除 [`Entry`](xref:Xamarin.Forms.Entry) 实例并刷新 `ListView` 中的数据之前将输入的数据保存到数据库中。

    > [!NOTE]
    > `OnAppearing` 方法覆盖的执行是在布局 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 后但在其变得可见之前进行的。 因此，这是设置 Xamarin.Forms 视图内容的好地方。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序  。

    输入多项数据，为每项数据点击 [`Button`](xref:Xamarin.Forms.Button)。 这会将数据保存到数据库，并使用所有数据库数据重新填充 [`ListView`](xref:Xamarin.Forms.ListView)：

    [![iOS 和 Android 上的本地 SQLite.NET 数据库数据暂留的屏幕截图](../images/consume-data-access-classes.png "本地数据库数据暂留")](../images/consume-data-access-classes-large.png#lightbox "本地数据库数据暂留")

    有关 Xamarin.Forms 中本地数据库的详细信息，请参阅 [Xamarin.Forms 本地数据库（指南）](~/xamarin-forms/data-cloud/data/databases.md)
