---
title: 演练：绑定 Android Kotlin 库
description: 本文提供了为现有 Kotlin 库 BubblePicker 创建 Xamarin Android 绑定的动手演练。 它介绍了一些主题，如编译 Kotlin 库、绑定和在 Xamarin Android 应用程序中使用绑定。
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519668"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>演练：绑定 Android Kotlin 库

Xamarin 使移动开发人员能够使用 Visual Studio 和C#创建跨平台的本机移动应用。 你可以使用现成的 Android 平台 SDK 组件，但在许多情况下，你还希望使用为该平台编写的第三方 Sdk，Xamarin 允许你通过绑定来实现此目的。 若要将第三方 Android 框架合并到你的 Xamarin Android 应用程序中，你需要先为其创建一个 Xamarin 的绑定，然后才能在应用程序中使用它。

Android 平台及其本机语言和工具不断发展，其中包括最近引入的 Kotlin 语言，最终将其设置为替换 Java。 有许多三维 Sdk Sdk，它们已从 Java 迁移到 Kotlin，并且为我们带来了新的挑战。 即使 Kotlin 绑定过程与 Java 相似，它也需要额外的步骤和配置设置，才能成功生成并运行 Xamarin Android 应用程序的一部分。  

本文档的目的是概述对此方案进行寻址的高级方法，并提供一个简单示例的详细分步指南。

## <a name="background"></a>背景

Kotlin 于2016年2月发布，并作为标准 Java 编译器的替代项定位到 2017 Android Studio。 稍后在2019中，Google 宣布 Kotlin 编程语言将成为适用于 Android 应用开发人员的首选语言。 高级绑定方法类似于[常规 Java 库的绑定过程](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)，其中包含几个重要的 Kotlin 特定步骤。

## <a name="prerequisites"></a>必备条件

为了完成本演练，您需要：

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 反编译程序](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>生成本机库

第一步是使用 Android Studio 生成本机 Kotlin 库。 库通常由第三方开发人员提供，或在[Google 的 Maven 存储库](https://maven.google.com/web/index.html)和其他远程存储库中提供。 例如，在本教程中，将创建气泡选取器 Kotlin 库的绑定：

![GitHub BubblePicker 演示](walkthrough-images/github-bubblepicker-demo.png)

1. 从 GitHub 下载适用于库的[源代码](https://github.com/igalata/Bubble-Picker/archive/develop.zip)，并将其解压缩到本地文件夹**气泡-选取器**。

1. 启动 Android Studio 并选择 "**打开现有 Android Studio 项目**" 菜单选项，选择 "气泡选取器" 本地文件夹：

    ![Android Studio 打开项目](walkthrough-images/android-studio-open-project.png)

1. 验证 Android Studio 是否是最新的，包括 Gradle。 源代码可以在 Android Studio v 3.5.3，Gradle v 5.4.1 上成功生成。 可在[此处找到](https://gradle.org/install/)有关如何将 Gradle 更新为最新的 Gradle 版本的说明。

1. 验证是否安装了所需的 Android SDK。 源代码需要 Android SDK v25。 打开**工具 > Sdk 管理器**菜单选项安装 sdk 组件。

1. 更新并同步位于项目文件夹的根目录中的**gradle**配置文件：

    - 将 Kotlin 版本设置为1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - 注册默认 Google 的 Maven 存储库，以便可以解决支持库依赖关系：

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - 配置文件更新后，它将不再同步，Gradle 将显示 "**立即同步**" 按钮，按下它并等待同步过程完成：

        ![立即 Android Studio Gradle 同步](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > Gradle 的依赖关系缓存可能已损坏，有时会在网络连接超时后发生。 重新下载依赖项和同步项目（需要网络）。

        > [!TIP]
        > Gradle 生成过程（守护程序）的状态可能已损坏。 停止所有 Gradle 守护程序可能会解决此问题。 停止 Gradle 生成过程（需要重新启动）。 对于损坏的 Gradle 进程，还可以尝试关闭 IDE，然后终止所有 Java 进程。

        > [!TIP]
        > 你的项目可能使用的是第三方插件，该插件与项目中的其他插件或项目请求的 Gradle 的版本不兼容。

1. 打开右侧的 "Gradle" 菜单，导航到 " **bubblepicker > 任务**" 菜单，通过双击来执行**生成**任务，并等待生成过程完成：

    ![Android Studio Gradle 执行任务](walkthrough-images/android-studio-gradle-execute-task.png)

1. 打开根文件夹文件浏览器并导航到 "生成" 文件夹： "**冒泡-选取器 > bubblepicker-> 生成-> 输出->** "，将**bubblepicker-release**文件保存为**bubblepicker-v 1.0. aar**，稍后将在绑定过程中使用此文件：

    ![Android Studio AAR 输出](walkthrough-images/android-studio-aar-output.png)

AAR 文件是 Android 存档，其中包含 Android 用此 SDK 运行应用程序所需的已编译 Kotlin 源代码和资产。  

## <a name="prepare-metadata"></a>准备元数据

第二步是准备由 Xamarin 使用的元数据转换文件，以生成相应C#的类。 Xamarin Android 绑定项目将发现给定 Android 存档中的所有本机类和成员，然后使用相应的元数据生成 XML 文件。 然后，手动创建的元数据转换文件将应用于以前生成的基线，以创建用于生成C#代码的最终 XML 定义文件。

元数据使用 [XPath](https://www.w3.org/TR/xpath/) 语法，由绑定生成器用来影响绑定程序集的创建。 [Java 绑定元数据](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)一文提供了有关转换的详细信息，可应用：

1. 创建一个空的**元数据 .xml**文件：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. 定义 xml 转换：

- 本机 Kotlin 库有两个依赖关系，您不想向其公开C# ，而是将两个转换定义为完全忽略它们。 重要说明：不会从生成的二进制文件中去除本机成员，只C#会生成类。 [Java 反编译程序](http://java-decompiler.github.io/)可用于识别依赖项。 运行该工具并打开前面创建的 AAR 文件。因此，将显示 Android 存档的结构，反映所有依赖项、值、资源、清单和类：  

    ![Java 反编译程序依赖项](walkthrough-images/java-decompiler-dependencies.png)

    使用 XPath 指令定义用于跳过处理这些包的转换：

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- 本机 `BubblePicker` 类有两个方法 `getBackgroundColor` 和 `setBackgroundColor`，并且以下转换会将其更改为C# `BackgroundColor` 属性：

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- 无符号类型 `UInt, UShort, ULong, UByte` 需要特殊处理。 对于这些类型，Kotlin 会自动更改方法名称和参数类型，这会在生成的代码中反映出来：

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    此代码编译为以下 Java 字节代码：

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    此外，相关类型（如 `UIntArray, UShortArray, ULongArray, UByteArray`）还受 Kotlin 的影响。 方法名称将更改为包含附加后缀，并将参数更改为同一类型的已签名版本元素数组。 在下面的示例中，类型 `UIntArray` 的参数会自动转换为 `int[]` 并且方法名称从 `fooUIntArrayMethod` 更改为 `fooUIntArrayMethod--ajY-9A`。 后一种方法是通过 Xamarin tools 发现的，并生成为有效的方法名称：

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    此代码编译为以下 Java 字节代码：

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    若要为其指定一个有意义的名称，可将以下元数据添加到**metadata .xml**中，这会将该名称更新回最初在 Kotlin 代码中定义的名称：

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    在 BubblePicker 示例中，没有使用无符号类型的成员，因此不需要进行额外的更改。

- 默认情况下，使用泛型参数的 Kotlin 成员转换为 Java 的参数。`Lang.Object` type。 例如，Kotlin 方法具有泛型参数 \<T >：

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    生成 Xamarin 后，该方法将C#按如下方式公开：

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Xamarin Kotlin 不支持 Java 和泛型，因此会创建一个通用C#方法来访问通用 API。 作为一种解决方法，你可以创建一个包装 Kotlin 库，并以强类型方式公开所需的 Api，而无需使用泛型。 另外，还可以通过强类型C# api 以相同的方式在端创建帮助程序以解决问题。

    > [!TIP]
    > 通过转换元数据，可以将任何更改应用到生成的绑定。 [绑定 Java 库](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)一文详细说明了如何生成和处理元数据。

## <a name="build-a-binding-library"></a>生成绑定库

下一步是使用 Visual Studio 绑定模板创建 Xamarin Android 绑定项目，添加所需的元数据和本机引用，然后生成项目以生成可执行的库：

1. 打开 Visual Studio for Mac 并创建新的 Xamarin Android 绑定库项目，为其指定一个名称，在本例中为**testBubblePicker**并完成向导。 Xamarin 绑定模板由以下路径定位： **android > 库 > 绑定库**：

    ![Visual Studio 创建绑定](walkthrough-images/visual-studio-create-binding.png)

    在转换文件夹中有三个主要转换文件：

    - **Metadata** -允许对最终 API 进行更改，如更改生成的绑定的命名空间。
    - **EnumFields** –包含 Java int 常量和C#枚举之间的映射。
    - **EnumMethods** -允许更改方法参数，并从 Java int 常量返回到C#枚举。

    将**EnumFields**和**EnumMethods**文件保持为空，并更新**元数据**以定义转换。

1. 将现有的**转换/元数据 .xml**文件替换为在上一步骤中创建的**元数据 .xml**文件。 在 "属性" 窗口中，验证 "文件**生成" 操作**是否设置为**TransformationFile**：

    ![Visual Studio 元数据](walkthrough-images/visual-studio-metadata.png)

1. 将在步骤1中生成的**bubblepicker-v 1.0. aar**文件作为本机引用添加到绑定项目。 若要添加本机库引用，请打开查找器，然后导航到 Android 存档文件夹。 将存档拖放到解决方案资源管理器中的 Jar 文件夹中。 或者，您可以使用 "Jar" 文件夹的 "**添加**上下文" 菜单选项，然后选择 "**现有文件 ...** "。 出于本演练的目的，请选择将文件复制到目录。 请确保将**生成操作**设置为**LibraryProjectZip**：

    ![Visual Studio 本机引用](walkthrough-images/visual-studio-native-reference.png)

1. 添加对[Kotlin. Stdlib.h NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)包的引用。 此包是 Kotlin 标准库的绑定。 如果不使用此包，则绑定仅在 Kotlin 库不使用任何 Kotlin 特定类型时才起作用，否则，所有这些成员都C#不会公开到，尝试使用绑定的任何应用都将在运行时崩溃。

    > [!TIP]
    > 由于 Xamarin 的限制，每个绑定项目只能添加一个 Android 存档（AAR）。 如果需要包含多个 AAR 文件，则需要多个 Xamarin 项目，每个 AAR 都有一个。 如果是本演练的这种情况，则必须为每个存档重复执行此步骤之前的四项操作。 作为另一种选择，可以将多个 Android 存档手动合并为单个存档，因此可以使用单个 Xamarin 绑定项目。

1. 最后一个操作是生成库，并且不会产生任何编译错误。 在编译错误的情况下，可以使用元数据 .xml 文件来处理和处理这些错误，这是你之前通过添加 xml 转换元数据创建的，它将添加、删除或重命名库成员。

## <a name="consume-the-binding-library"></a>使用绑定库

最后一步是在 Xamarin Android 应用程序中使用 Xamarin 的绑定库。 创建新的 Xamarin Android 项目，添加对绑定库的引用并呈现气泡选取器 UI：

1. 创建 Xamarin Android 项目。 使用**android > 应用 > Android 应用**程序作为起点，选择 "**最新" 和 "最高**" 作为 "面向平台" 选项，以避免兼容性问题。 以下所有步骤均面向此项目：

    ![Visual Studio 创建应用](walkthrough-images/visual-studio-create-app.png)

1. 添加对绑定项目的项目引用，或添加前面创建的 DLL 的引用：

    ![Visual Studio 添加绑定引用 .png](walkthrough-images/visual-studio-add-binding-reference.png)

1. 添加对已添加到早期版本的 Xamarin 绑定项目中的[Kotlin. Stdlib.h NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)包的引用。 它添加了对需要在运行时中处理的任何 Kotlin 特定类型的支持。  如果没有此包，则可以编译应用程序，但在运行时将崩溃：

    ![Visual Studio 添加 Stdlib.h NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. 将 `BubblePicker` 控件添加到 `MainActivity`的 Android 布局。 打开**testBubblePicker/Resources/layout/content_main .xml**文件，并将 BubblePicker 控件节点附加为根 RelativeLayout 控件的最后一个元素：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. 更新应用程序的源代码，并将初始化逻辑添加到 `MainActivity`，这会激活气泡选取器 SDK：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter` 和 `BubblePickerListener` 是从头开始创建的两个类，用于处理气泡数据和控件交互：

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. 运行应用程序，该应用程序应呈现气泡选取器 UI：

    ![BubblePicker 演示](walkthrough-images/bubble-picker-demo.png)

    该示例需要附加代码来呈现元素样式并处理交互，但 `BubblePicker` 控件已成功创建和激活。

祝贺你！ 你已成功创建了一个 Xamarin Android 应用和一个使用 Kotlin 库的绑定库。  

现在，应该具有使用本机 Kotlin 库的基本 Xamarin 应用程序，该应用程序通过 Xamarin 绑定库使用。 本演练有意使用一个基本示例来更好地强调所引入的主要概念。 在实际方案中，您可能需要公开更多的 Api，并向它们应用元数据转换。

## <a name="related-links"></a>相关链接

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安装](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 反编译程序](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 库](https://github.com/igalata/Bubble-Picker)
- [绑定 Java 库](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Java 绑定元数据](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Kotlin. Stdlib.h NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [示例项目存储库](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
