---
title: 材料主题
description: 如何通过材料主题为 Xamarin Android 应用提供主题
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 809f6241b3a17f63fe3077f896095c303e1dfd2e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916729"
---
# <a name="material-theme"></a>材料主题

*材料主题*是一种用户界面样式，用于确定从 Android 5.0 （棒糖）开始的视图和活动的外观。 材料主题内置于 Android 5.0 中，因此它由系统 UI 和应用程序使用。 对于系统范围内的外观选项，用户可从 "设置" 菜单动态选择的 "材料" 主题不是 "主题"。 相反，可以将材料主题视为一组相关的内置基准样式，您可以使用这些样式自定义应用程序的外观。

Android 提供三种材料主题风格：

- 材料主题 &ndash; 深色版本 `Theme.Material`;这是 Android 5.0 中的默认风格。

- 材料主题 &ndash; 轻型版本 `Theme.Material.Light`。

- `Theme.Material.Light.DarkActionBar` 材料主题 &ndash; 轻型版本，但具有深色操作栏。

此处显示了这些材料主题风格的示例：

[![深色主题、浅色主题和深色操作栏主题的示例屏幕截图](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

可以从材料主题派生，以创建自己的主题，并覆盖部分或全部颜色属性。 例如，你可以创建一个从 `Theme.Material.Light`派生的主题，但会覆盖应用栏颜色以匹配品牌的颜色。 您还可以设置各个视图的样式;例如，可以创建具有更多圆角的[CardView](~/android/user-interface/controls/card-view.md)样式，并使用较暗的背景色。

你可以对整个应用使用单个主题，也可以在应用中对不同屏幕（活动）使用不同的主题。 例如，在上面的屏幕截图中，一个应用对每个活动使用不同的主题来演示内置的配色方案。 单选按钮会将应用切换到不同的活动，因此会显示不同的主题。

由于仅 Android 5.0 及更高版本支持材料主题，因此你不能使用它（或从材料主题派生的自定义主题）来为你的应用程序提供应用程序的主题，使其在早期版本的 Android 上运行。 但是，你可以将应用配置为使用 Android 5.0 设备上的材料主题，并在较早版本的 Android 上运行时适当地回退到前面的主题（有关详细信息，请参阅本文的 "[兼容性](#compatibility)" 部分）。

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用新的 Android 5.0 材料主题功能，需要满足以下要求：

- **Xamarin** &ndash; xamarin 4.20 或更高版本必须安装并配置 Visual Studio 或 Visual Studio for Mac。 

- 必须通过 Android SDK 管理器安装**Android SDK** &ndash; Android 5.0 （API 21）或更高版本。

- 如果特别面向 API 级别23和更早版本，可以使用**JAVA jdk 1.8** &ndash; JDK 1.7。 JDK 1.8 可从[Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)获得。

若要了解如何配置 Android 5.0 应用项目，请参阅[设置 android 5.0 项目](~/android/platform/lollipop.md)。

## <a name="using-the-built-in-themes"></a>使用内置主题

使用材料主题的最简单方法是将应用程序配置为使用无需自定义的内置主题。 如果你不希望显式配置主题，你的应用将默认为 `Theme.Material` （深色主题）。 如果应用只有一个活动，可以在活动级别配置主题。 如果应用有多个活动，则可以在应用程序级别配置主题，使其在所有活动中使用相同的主题，也可以为不同的活动分配不同的主题。 以下部分介绍如何在应用级别和活动级别配置主题。

### <a name="theming-an-application"></a>主题应用程序

若要将整个应用程序配置为使用材料主题风格，请将**androidmanifest.xml**中的应用程序节点的 `android:theme` 属性设置为以下其中一项：

- `@android:style/Theme.Material` &ndash; 深色主题。

- `@android:style/Theme.Material.Light` &ndash; 浅色主题。

- `@android:style/Theme.Material.Light.DarkActionBar` 具有深色操作栏的 &ndash; 浅色主题。

下面的示例将 application *MyApp*配置为使用浅色主题：

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者，可以在**AssemblyInfo.cs** （或**Properties.cs**）中设置应用程序 `Theme` 属性。 例如：

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

当应用程序主题设置为 `@android:style/Theme.Material.Light`时， *MyApp*中的每个活动都将使用 `Theme.Material.Light`显示。

### <a name="theming-an-activity"></a>主题活动

若要为活动设置主题，请向活动声明之上的 `[Activity]` 属性添加 `Theme` 设置，并将 `Theme` 分配给要使用的材料主题风格。 下面的示例使用 `Theme.Material.Light`的活动主题：

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此应用中的其他活动将使用默认的 `Theme.Material` 深色配色方案（如果配置了应用程序主题设置，则为）。

<a name="customtheme" />

## <a name="using-custom-themes"></a>使用自定义主题

可以通过创建一个自定义主题来为你的应用程序使用品牌&rsquo;的颜色来增强你的品牌。 若要创建自定义主题，请定义从内置材料主题风格派生的新样式，并覆盖要更改的颜色属性。 例如，您可以定义从 `Theme.Material.Light.DarkActionBar` 派生的自定义主题，并将屏幕背景色更改为米色而不是白色。

材料主题公开以下用于自定义的布局属性：

- `colorPrimary` &ndash; 应用栏的颜色。

- `colorPrimaryDark` &ndash; 状态栏和上下文应用栏的颜色;这通常是 `colorPrimary`的深色版本。

- `colorAccent` &ndash; UI 控件（如复选框、单选按钮和编辑文本框）的颜色。

- `windowBackground` &ndash; 屏幕背景的颜色。

- `textColorPrimary` &ndash; 应用栏中 UI 文本的颜色。

- `statusBarColor` &ndash; 状态栏的颜色。

- `navigationBarColor` &ndash; 导航栏的颜色。

以下关系图中标记了这些屏幕区域：

[属性的 ![关系图及其关联的屏幕区域](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

默认情况下，`statusBarColor` 设置为 `colorPrimaryDark`的值。 您可以将 `statusBarColor` 设置为纯色，也可以将其设置为 `@android:color/transparent`，使状态栏成为透明的。 还可以通过将 `navigationBarColor` 设置为 `@android:color/transparent`来使导航栏是透明的。

### <a name="creating-a-custom-app-theme"></a>创建自定义应用主题

可以通过在应用项目的 "**资源**" 文件夹中创建和修改文件来创建自定义应用主题。 若要为应用程序定义自定义主题，请使用以下步骤：

- 在 "**资源/值**" 中创建一个**彩色 .xml**文件 &mdash; 使用此文件来定义自定义主题颜色。 例如，可以将以下代码粘贴到**colors**中，以帮助您入门：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

- 修改此示例文件以定义将在自定义主题中使用的颜色资源的名称和颜色代码。

- 创建**资源/值-v21**文件夹。 在此文件夹中，创建一个**样式 .xml**文件：

    [Resources/values-21 文件夹中样式 .xml 的位置 ![](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    请注意， **v21**特定于 android 5.0 &ndash; 较早版本的 android 将不会读取此文件夹中的文件。

- 将 `resources` 节点添加到**styles .xml** ，并使用自定义主题的名称定义 `style` 节点。 例如，下面是一个用于定义*MyCustomTheme*的 style **.xml**文件（派生自内置 `Theme.Material.Light` 主题样式）：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

- 此时，使用*MyCustomTheme*的应用程序将显示 stock `Theme.Material.Light` 主题，无需自定义：

    [自定义之前 ![自定义主题外观](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

- 通过定义要更改的布局特性的颜色，将颜色自定义添加到**样式 .xml。** 例如，若要将应用栏颜色更改为 "`my_blue`"，并将 UI 控件的颜色更改为 "`my_purple`，请将" 颜色重写 "添加到"**样式**.xml "，这是指在 color **.xml**中配置的颜色资源：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

进行这些更改后，使用*MyCustomTheme*的应用程序将在 `my_purple`中 `my_blue` 和 UI 控件中显示应用程序栏颜色，但在其他任何位置使用 `Theme.Material.Light` 配色方案：

[自定义后 ![自定义主题外观](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

在此示例中， *MyCustomTheme*从背景色、状态栏和文本颜色的 `Theme.Material.Light` 中借用颜色，但会将应用栏的颜色更改为 `my_blue` 并将单选按钮的颜色设置为 "`my_purple`"。

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>创建自定义视图样式

Android 5.0 还允许您为单个视图建立样式。 创建**colors**和 style **.xml**后（如上一节所述），可以将视图样式添加到 style **.xml**。
若要为单个视图的样式，请使用以下步骤：

- 编辑**资源/values-v21/style .xml**并添加一个 `style` 节点，其名称为自定义视图样式的名称。 在此 `style` 节点中为视图设置自定义颜色属性。 例如，若要创建具有更多圆角的自定义[CardView](~/android/user-interface/controls/card-view.md)样式，并使用 `my_blue` 作为卡片背景色，请将 `style` 节点添加到 style **.xml** （在 `resources` 节点内），并配置背景色和角半径：

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

- 在布局中，为该视图设置 `style` 属性，使之与你在上一步中选择的自定义样式名称匹配。 例如：

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下面的屏幕截图提供默认 `CardView` （显示在左侧）的示例，与已使用自定义 `CardView.MyBlue` 主题进行样式化的 `CardView` （在右侧显示）相同：

[默认 CardView 和自定义 CardView ![示例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

在此示例中，将显示自定义 `CardView`，其中背景颜色 `my_blue` 和18dp 角半径。

## <a name="compatibility"></a>兼容性

若要为应用程序应用格式，使其在 Android 5.0 上使用材料主题，但会自动恢复到早期版本的 Android 版本的向下兼容样式，请使用以下步骤：

- 在**资源/values-v21/style .xml**中定义从材料主题样式派生的自定义主题。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- 在**资源/值/样式 .xml**中定义派生自旧版主题的自定义主题，但使用与上面相同的主题名称。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- 在**androidmanifest.xml**中，配置应用的自定义主题名称。 
    例如：

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

- 或者，您可以使用自定义主题来为特定活动指定样式：

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果主题使用了 color **.xml**文件中定义的颜色，请确保将此文件放在**资源/值**（而不是**v21**）中，以便您的自定义主题的两个版本都可以访问您的颜色定义。

当你的应用在 Android 5.0 设备上运行时，它将使用**资源/values-v21/样式**中指定的主题定义。 在较旧的 Android 设备上运行此应用时，它将自动回退到 "**资源/值"/"样式 .xml**" 中指定的主题定义。

有关与较旧的 Android 版本的主题兼容性的详细信息，请参阅[备用资源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>总结

本文介绍了 Android 5.0 （棒糖形）中包含的新材料主题用户界面样式。 本文介绍了三种内置的材料主题风格，您可以使用它们来设计您的应用程序的样式，还介绍了如何创建用于标记您的应用程序的自定义主题，并提供了如何为单个视图提供主题的示例。 最后，本文介绍了如何在应用中使用材料主题，同时保持与早期版本的 Android 的向下兼容性。

## <a name="related-links"></a>相关链接

- [ThemeSwitcher （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [棒糖形简介](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [备用资源](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android 棒糖形](https://developer.android.com/about/versions/lollipop)
- [Android 饼图开发人员](https://developer.android.com/about/versions/pie/)
- [材料设计](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [材料设计原则](https://material.io/design/)
- [维护兼容性](https://developer.android.com/training/backward-compatible-ui/)
