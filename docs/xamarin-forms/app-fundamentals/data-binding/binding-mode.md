---
title: Xamarin.Forms 绑定模式
description: 本文介绍如何使用绑定模式控制源和目标之间的信息流，绑定模式由 BindingMode 枚举的成员指定。 每个可绑定属性都有一个默认绑定模式，该模式指示该属性是数据绑定目标时有效的模式。
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
ms.openlocfilehash: 3bf1ab647faa4b6c4735585ddfeaeb704d7d3f41
ms.sourcegitcommit: 6d86aac422d6ce2131930d18ada161d117c8c61b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77567073"
---
# <a name="xamarinforms-binding-mode"></a>Xamarin.Forms 绑定模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

在[上一篇文章](basic-bindings.md)中，“替代代码绑定”和“替代 XAML 绑定”页面都有一个 `Label`，其 `Scale` 属性绑定到 `Slider` 的 `Value` 属性   。 由于 `Slider` 初始值为 0，所以这导致 `Label` 的 `Scale` 属性被设置为 0 而不是 1，并且 `Label` 消失。

在 [DataBindingDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) 示例中，“反向绑定”页面类似于上一篇文章中的程序，只是数据绑定是针对 `Slider` 而非 `Label` 定义的   ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label"
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

首先，这似乎是反向的：现在，`Label` 是数据绑定源，而 `Slider` 是目标。 绑定引用 `Label` 的 `Opacity` 属性，其默认值为 1。

正如你所料，`Slider` 从 `Label` 的初始 `Opacity` 值初始化为值 1。 如左侧的 iOS 屏幕截图中所示：

[![反向绑定](binding-mode-images/reversebinding-small.png "反向绑定")](binding-mode-images/reversebinding-large.png#lightbox "反向绑定")

但是，正如 Android 屏幕截图所示，`Slider` 会继续工作，你可能会对此感到惊讶。 这似乎表明，当 `Slider`（而非 `Label`）是绑定目标时，数据绑定效果更好，因为初始化的工作方式与我们预期的一样。

反向绑定示例与早期示例之间的差异涉及绑定模式   。

## <a name="the-default-binding-mode"></a>默认绑定模式

使用 [`BindingMode`](xref:Xamarin.Forms.BindingMode) 枚举的成员指定绑定模式：

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; 数据在源和目标之间双向传输
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; 数据从源到目标单向传输
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; 数据从目标到源单向传输
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; 只有在 `BindingContext` 更改时，数据才从源到目标单向传输（Xamarin.Forms 3.0 新增功能）

每个可绑定属性都有一个默认绑定模式，该模式在创建可绑定属性时进行设置，并且可从 `BindableProperty` 对象的 [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) 属性中获得。 此默认绑定模式指示该属性是数据绑定目标时有效的模式。

大多数属性（如 `Rotation`、`Scale` 和 `Opacity`）的默认绑定模式都是 `OneWay`。 如果这些属性是数据绑定目标，则从源设置目标属性。

但是，`Slider` 的 `Value` 属性的默认绑定模式为 `TwoWay`。 这意味着，如果 `Value` 属性是数据绑定目标时，则通常从源设置目标，但也可从目标设置源。 这就是允许从初始 `Opacity` 值设置 `Slider` 的原因。

这种双向绑定似乎会创建一个无限循环，但这种情况不会发生。 除非属性实际发生变化，否则可绑定属性不会发出属性更改的信号。 这样可以避免无限循环。

### <a name="two-way-bindings"></a>双向绑定

大多数可绑定属性的默认绑定模式都是 `OneWay`，但以下属性的默认绑定模式为 `TwoWay`：

- `DatePicker` 的 `Date` 属性
- `Editor`、`Entry`、`SearchBar` 和 `EntryCell` 的 `Text` 属性
- `ListView` 的 `IsRefreshing` 属性
- `MultiPage` 的 `SelectedItem` 属性
- `Picker` 的 `SelectedIndex` 和 `SelectedItem` 属性
- `Slider` 和 `Stepper` 的 `Value` 属性
- `Switch` 的 `IsToggled` 属性
- `SwitchCell` 的 `On` 属性
- `TimePicker` 的 `Time` 属性

这些特定属性被定义为 `TwoWay`，理由非常充分：

当数据绑定与模型-视图-视图模型 (MVVM) 应用程序体系结构一起使用时，ViewModel 类是数据绑定源，而由 `Slider` 等视图组成的 View 则是数据绑定目标。 MVVM 绑定更类似于反向绑定示例，而不是之前示例中的绑定  。 你很可能会想要使用 ViewModel 中相应属性的值来初始化页面上的每个视图，但视图中的更改应该也会影响 ViewModel 属性。

默认绑定模式为 `TwoWay` 的属性是最有可能在 MVVM 方案中使用的属性。

### <a name="one-way-to-source-bindings"></a>单向数据源绑定

只读可绑定属性的默认绑定模式为 `OneWayToSource`。 只有一个读/写可绑定属性的默认绑定模式为 `OneWayToSource`：

- `ListView` 的 `SelectedItem` 属性

其基本原理是，对 `SelectedItem` 属性的绑定应该导致设置绑定源。 本文后面的示例将替代该行为。

### <a name="one-time-bindings"></a>一次性绑定

许多属性（包括 `Entry` 的 `IsTextPredictionEnabled` 属性）都具有 `OneTime` 的默认绑定模式。

只有在绑定上下文更改时，才会更新绑定模式为 `OneTime` 的目标属性。 对于这些目标属性的绑定，该模式简化了绑定基础结构，因为不必监视源属性中的更改。

## <a name="viewmodels-and-property-change-notifications"></a>ViewModel 和属性更改通知

“简易颜色选择器”页面演示了如何使用简单的 ViewModel  。 数据绑定允许用户使用三个 `Slider` 元素为 Hue、Saturation 和 Luminosity 选择颜色。

ViewModel 是数据绑定源。 ViewModel 没有定义可绑定属性，但它实现了一种通知机制，允许在属性值更改时通知绑定基础结构  。 此通知机制是 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 接口，该接口定义名为 [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) 的单个事件。 实现此接口的类通常在其某个公共属性更改值时触发该事件。 如果属性永远不会更改，则不需要触发该事件。 （`INotifyPropertyChanged` 接口也由 `BindableObject` 实现，并且只要可绑定属性更改值，就会触发 `PropertyChanged` 事件。）

`HslColorViewModel` 类定义五个属性：`Hue`、`Saturation`、`Luminosity` 和 `Color` 属性是相互关联的。 当这三个颜色组件中的任何一个更改值时，都将重新计算 `Color` 属性，并为所有四个属性触发 `PropertyChanged` 事件：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

当 `Color` 属性更改时，`NamedColor` 类中的静态 `GetNearestColorName` 方法（也包含在 DataBindingDemos 解决方案中）获取命名最类似的颜色并设置 `Name` 属性  。 此 `Name` 属性具有专用 `set` 访问器，因此无法从类之外进行设置。

将 ViewModel 设置为绑定源时，绑定基础结构会将处理程序附加到 `PropertyChanged` 事件。 通过这种方式，绑定可收到属性的更改通知，然后可以根据更改的值设置目标属性。

但是，当目标属性（或目标属性的 `Binding` 定义）的 `BindingMode` 为 `OneTime` 时，绑定基础结构不必在 `PropertyChanged` 事件上附加处理程序。 目标属性仅在 `BindingContext` 更改时更新，而不是在源属性本身更改时更新。

“简易颜色选择器”XAML 文件实例化页面资源字典中的 `HslColorViewModel` 并初始化 `Color` 属性  。 `Grid` 的 `BindingContext` 属性被设置为 `StaticResource` 绑定扩展以引用该资源：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel"
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />

            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

`BoxView`、`Label` 和三个 `Slider` 视图从 `Grid` 继承绑定上下文。 这些视图都是引用 ViewModel 中的源属性的绑定目标。 对于 `BoxView` 的 `Color` 属性和 `Label` 的 `Text` 属性，数据绑定是 `OneWay`：从 ViewModel 中的属性中设置视图中的属性。

但是，`Slider` 的 `Value` 属性为 `TwoWay`。 此模式允许从 ViewModel 设置每个 `Slider`，也允许从每个 `Slider` 设置 ViewModel。

首次运行程序时，`BoxView`、`Label` 和三个 `Slider` 元素均根据实例化 ViewModel 时设置的初始 `Color` 属性集从 ViewModel 中进行设置。 如左侧 iOS 屏幕截图中所示：

[![简易颜色选择器](binding-mode-images/simplecolorselector-small.png "简易颜色选择器")](binding-mode-images/simplecolorselector-large.png#lightbox "简易颜色选择器")

操纵滑块时，`BoxView` 和 `Label` 会相应地更新，如 Android 屏幕截图所示。

在资源字典中实例化 ViewModel 是一种常见的方法。 也可以在 `BindingContext` 属性的属性元素标记内实例化 ViewModel。 在“简易颜色选择器”XAML 文件中，尝试从资源字典中删除 `HslColorViewModel` 并将其设置为 `Grid` 的 `BindingContext` 属性，如下所示  ：

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

可以采用多种方式设置绑定上下文。 有时，代码隐藏文件会实例化 ViewModel 并将其设置为页面的 `BindingContext` 属性。 这些都是有效的方法。

## <a name="overriding-the-binding-mode"></a>替代绑定模式

如果目标属性的默认绑定模式不适合特定的数据绑定，则可以通过将 `Binding` 的 [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) 属性（或 `Binding` 标记扩展的 [`Mode`](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) 属性）设置为 `BindingMode` 枚举的其中一个成员来替代它。

但是，将 `Mode` 属性设置为 `TwoWay` 并不总是像你预期的那样有效。 例如，尝试修改“替代 XAML 绑定”XAML 文件，以在绑定定义中包含 `TwoWay`  ：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

这预计会将 `Slider` 初始化为 `Scale` 属性的初始值（即 1），但情况并不是这样。 初始化 `TwoWay` 绑定时，首先从源设置目标，这意味着会将 `Scale` 属性设置为 `Slider` 默认值 0。 对 `Slider` 设置 `TwoWay` 绑定时，最初会从源设置 `Slider`。

可以在“替代 XAML 绑定”示例中将绑定模式设置为 `OneWayToSource`  ：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

现在，`Slider` 被初始化为 1（`Scale` 的默认值），但操作 `Slider` 并不会影响 `Scale` 属性，因此这不是很有用。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement) 类还定义了 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 属性，这些属性可以在水平和垂直方向上以不同方式缩放 `VisualElement`。

使用 `TwoWay` 替代默认绑定模式的一种非常有用的应用程序涉及 `ListView` 的 `SelectedItem` 属性。 默认绑定模式为 `OneWayToSource`。 如果对 `SelectedItem` 属性设置数据绑定以引用 ViewModel 中的源属性，则从 `ListView` 选择设置该源属性。 但是，在某些情况下，可能还需要从 ViewModel 初始化 `ListView`。

“示例设置”页面演示了此方法  。 此页面表示应用程序设置的简单实现，这些设置通常在 ViewModel 中定义，例如此 `SampleSettingsViewModel` 文件：

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

每个应用程序设置都是一个属性，保存在名为 `SaveState` 的方法中的 Xamarin.Forms 属性字典中，并从该字典加载到构造函数中。 类的底部有两种方法可以帮助简化 ViewModel，并使它们不易出错。 底部的 `OnPropertyChanged` 方法有一个设置为调用属性的可选参数。 这可以避免在将属性的名称指定为字符串时出现拼写错误。

类中的 `SetProperty` 方法要更胜一筹：它将设置的属性值与存储为字段的值进行比较，并且仅当两个值不相等时才调用 `OnPropertyChanged`。

`SampleSettingsViewModel` 类为背景色定义了两个属性：`BackgroundNamedColor` 属性属于类型 `NamedColor`，这也是 DataBindingDemos  解决方案中包含的一个类。 `BackgroundColor` 属性属于类型 `Color`，并且该属性获取自 `NamedColor` 对象的 `Color` 属性。

`NamedColor` 类使用 .NET 反射来枚举 Xamarin.Forms `Color` 结构中的所有静态公共字段，并将它们以及它们的名称存储在可从静态 `All` 属性访问的集合中：

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

DataBindingDemos 项目中的 `App` 类定义类型为 `SampleSettingsViewModel` 且名为 `Settings` 的属性  。 在实例化 `App` 类时初始化此属性，并在调用 `OnSleep` 方法时调用 `SaveState` 方法：

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

有关应用程序生命周期方法的详细信息，请参阅[应用生命周期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)一文  。

几乎所有其他内容都在 SampleSettingsPage.xaml 文件中进行处理  。 使用 `Binding` 标记扩展来设置页面的 `BindingContext`：绑定源是静态 `Application.Current` 属性，它是项目中 `App` 类的实例，而 `Path` 设置为 `Settings` 属性，它是 `SampleSettingsViewModel` 对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

页面的所有子项均继承绑定上下文。 此页面上的大多数其他绑定都是 `SampleSettingsViewModel` 中的属性。 `BackgroundColor` 属性用于设置 `StackLayout` 的 `BackgroundColor` 属性，而 `Entry`、`DatePicker`、`Switch` 和 `Stepper` 属性均被绑定到 ViewModel 中的其他属性。

`ListView` 的 `ItemsSource` 属性被设置为静态 `NamedColor.All` 属性。 这会使用所有 `NamedColor` 实例来填充 `ListView`。 对于 `ListView` 中的每个项，项的绑定上下文都被设置为 `NamedColor` 对象。 `ViewCell` 中的 `BoxView` 和 `Label` 被绑定到 `NamedColor` 中的属性。

`ListView` 的 `SelectedItem` 属性属于类型 `NamedColor`，并被绑定到 `SampleSettingsViewModel` 的 `BackgroundNamedColor` 属性：

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

`SelectedItem` 的默认绑定模式为 `OneWayToSource`，它从所选项目中设置 ViewModel 属性。 `TwoWay` 模式允许从 ViewModel 中初始化 `SelectedItem`。

但是，以这种方式设置 `SelectedItem` 时，`ListView` 不会自动滚动，因此无法显示所选项目。 代码隐藏文件中有必要存在一些代码：

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem,
                                   ScrollToPosition.MakeVisible,
                                   false);
        }
    }
}
```

左侧的 iOS 屏幕截图显示了程序首次运行时的情况。 `SampleSettingsViewModel` 中的构造函数将背景色初始化为白色，即 `ListView` 中选择的颜色：

[![示例设置](binding-mode-images/samplesettings-small.png "示例设置")](binding-mode-images/samplesettings-large.png#lightbox "示例设置")

其他屏幕截图显示已更改的设置。 尝试使用此页面时，请记住将程序置于休眠状态，或在程序运行的设备或模拟器上终止程序。 从 Visual Studio 调试器终止程序不会导致调用 `App` 类中的 `OnSleep` 替代。

在下一篇文章中，可了解如何指定对 `Label` 的 `Text` 属性设置的数据绑定的[字符串格式](string-formatting.md)  。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 书中的数据绑定章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
