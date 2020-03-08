---
title: 绑定目标-C 库
description: 本文档提供了有关如何创建C#到目标 C 代码的绑定的高级概述，并介绍了如何绑定事件、方法、自定义控件等。
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 6841e94ad13357c51e6ccf59e35c659dfb9954aa
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914630"
---
# <a name="binding-objective-c-libraries"></a>绑定目标-C 库

使用 Xamarin 或 Xamarin 时，可能会遇到要使用第三方目标-C 库的情况。 在这些情况下，可以使用 Xamarin 绑定项目创建到本机C#目标 C 库的绑定。 该项目使用的工具与用于将 iOS 和 Mac Api 引入到C#的工具相同。

本文档介绍如何绑定目标 C Api，如果只是要绑定 C Api，则应将标准 .NET 机制用于此操作，即[P/Invoke framework](https://www.mono-project.com/docs/advanced/pinvoke/)。
"[链接本机库](~/ios/platform/native-interop.md)" 页上提供了有关如何静态链接 C 库的详细信息。

请参阅我们的[绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)。
此外，如果你想要了解有关该问题的详细信息，请查看我们的[绑定概述](~/cross-platform/macios/binding/overview.md)页。

可以为 iOS 和 Mac 库生成绑定。
本页介绍如何使用 iOS 绑定，但 Mac 绑定非常相似。

**IOS 的示例代码**

您可以使用[IOS 绑定示例](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample)项目来试验绑定。

<a name="Getting_Started" />

## <a name="getting-started"></a>入门

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

创建绑定的最简单方法是创建一个 Xamarin iOS 绑定项目。
可以通过选择项目类型 " **iOS > 库" > 绑定库**来执行 Visual Studio for Mac 此操作：

[![](objective-c-libraries-images/00-sml.png "Do this from Visual Studio for Mac by selecting the project type, iOS Library Bindings Library")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

创建绑定的最简单方法是创建一个 Xamarin iOS 绑定项目。
可以通过以下方式从 Visual Studio on Windows 执行此操作：选择项目类型，  **C# visual > IOS > 绑定库（ios）** ：

[![](objective-c-libraries-images/00vs-sml.png "iOS Bindings Library iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 注意：仅 Visual Studio for Mac 支持**Xamarin**的绑定项目。

-----

生成的项目包含一个可以编辑的小模板，其中包含两个文件： `ApiDefinition.cs` 和 `StructsAndEnums.cs`。

`ApiDefinition.cs` 是用于定义 API 协定的位置，这是描述如何将基础目标 C API 投影到C#的文件。 此文件的语法和内容是本文档讨论的主要主题，其中的内容仅限于C#接口和C#委托声明。 `StructsAndEnums.cs` 文件是你将在其中输入接口和委托所需的任何定义的文件。 这包括你的代码可能使用的枚举值和结构。

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>绑定 API

若要执行综合性绑定，你需要了解目标-C API 定义，并熟悉 .NET Framework 设计准则。

若要绑定库，通常会开始使用 API 定义文件。 API 定义文件只是一个C#源文件，其中包含C#的接口已使用有助于驱动绑定的少量属性进行批注。  此文件定义了和目标-C 之间C#的协定。

例如，以下是一个适用于库的简单 api 文件：

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

上面的示例定义了一个名为 `Cocos2D.Camera` 的类，该类派生自 `NSObject` 基类型（此类型来自 `Foundation.NSObject`）并定义一个静态属性（`ZEye`）、两个不采用参数的方法和一个采用三个参数的方法。

下面的[api 定义文件](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)部分中介绍了 api 文件的格式和可使用的属性。

若要生成完整的绑定，通常需要处理四个组件：

- API 定义文件（`ApiDefinition.cs` 模板中）。
- 可选： API 定义文件（在模板中`StructsAndEnums.cs`）所需的任何枚举、类型、结构。
- 可选：额外的源，可以扩展生成的绑定，或提供更C#友好的 API （ C#添加到项目的任何文件）。
- 要绑定的本机库。

此图显示了文件之间的关系：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "This chart shows the relationship between the files")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

API 定义文件将只包含命名空间和接口定义（具有接口可以包含的任何成员），不应包含类、枚举、委托或结构。 API 定义文件只是将用于生成 API 的协定。

枚举或支持类所需的任何额外代码都应托管在单独的文件中，在上面的示例中，"CameraMode" 是一个枚举值，该枚举值在 CS 文件中不存在，应在单独的文件中托管，例如 `StructsAndEnums.cs`：

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs` 文件与 `StructsAndEnum` 类结合使用，用于生成库的核心绑定。 您可以按原样使用生成的库，但通常情况下，您需要优化生成的库，以便为用户C#的权益添加某些功能。 一些示例包括实现 `ToString()` 方法、提供C#索引器、向和来自某些本机类型的隐式转换或提供某些方法的强类型版本。 这些改进存储在额外C#文件中。 只需将C#文件添加到项目，这些文件就会包含在此生成过程中。

这会显示如何实现 `Extra.cs` 文件中的代码。 请注意，你将使用分部类，因为这会增加从 `ApiDefinition.cs` 和 `StructsAndEnums.cs` 核心绑定的组合中生成的分部类：

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

生成库将生成本机绑定。

若要完成此绑定，应将本机库添加到项目。  为此，可以将本机库添加到项目中，方法是在解决方案资源管理器中将本机库拖放到项目中，或者通过右键单击项目并选择 "**添加**" > "**添加文件**" 以选择本机库。
本机库按约定从单词 "lib" 开始，以扩展名 ". a" 结尾。 当你执行此操作时，Visual Studio for Mac 将添加两个文件：。一个文件和一个C#自动填充的文件，其中包含有关本机库包含的内容的信息：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Native libraries by convention start with the word lib and end with the extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

`libMagicChord.linkwith.cs` 文件的内容包含有关如何使用此库的信息，并指示 IDE 将此二进制文件打包到生成的 DLL 文件中：

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

有关如何使用[`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)的完整详细信息 
属性在[绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)中进行了介绍。

现在，生成项目时，您将得到一个 `MagicChords.dll` 文件，该文件同时包含绑定和本机库。 您可以将此项目或生成的 DLL 分发给其他开发人员，以供自己使用。

有时，你可能会发现需要几个枚举值、委托定义或其他类型。 不要将它们放在 API 定义文件中，因为这只是一个约定

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 定义文件

API 定义文件包含多个接口。 API 定义中的接口将转换为类声明，必须使用[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)特性修饰这些接口，才能指定类的基类。

您可能想知道为什么不使用类而不是协定定义的接口。 我们选取了接口，因为它允许我们编写方法的协定，而无需在 API 定义文件中提供方法体，也不必提供必须引发异常或返回有意义的值的主体。

但由于我们使用接口作为一个框架来生成类，因此必须使用特性来修饰协定的各个部分，以驱动绑定。

<a name="Binding_Methods" />

### <a name="binding-methods"></a>绑定方法

最简单的绑定是绑定方法。 只需使用C#命名约定在接口中声明方法，并使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)来修饰方法
属性中。 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性可将C#名称与 Xamarin 运行时中的目标-C 名称进行链接。 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)的参数 
attribute 是目标-C 选择器的名称。 以下是一些示例：

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

上面的示例演示如何绑定实例方法。 若要绑定静态方法，必须使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)特性，如下所示：

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

这是必需的，因为协定是接口的一部分，并且接口没有静态和实例声明的概念，因此，需要再次使用特性。 如果要从绑定中隐藏某个特定的方法，可以使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)特性来修饰该方法。

`btouch-native` 命令将介绍引用参数是否不为 null。 如果要为特定参数允许空值，请使用[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
参数上的特性，如下所示：

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)关键字导出引用类型时，还可以指定分配语义。 这是确保不泄露数据所必需的。

<a name="Binding_Properties" />

### <a name="binding-properties"></a>绑定属性

与方法一样，使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)来绑定目标-C 属性
属性并直接映射到C#属性。 与方法一样，可以用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)修饰属性
和[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
属性定义。

当你在 btouch 上的属性中使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性时，实际上会绑定两种方法： getter 和 setter。 你为导出提供的名称是**basename** ，而 setter 是通过预先计算 "set" 一词来计算的，将**basename**的第一个字母变为大写，并使选择器采用参数。 这意味着，应用于属性的 `[Export ("label")]` 实际上绑定了 "label" 和 "setLabel：" 目标 C 方法。

有时，目标-C 属性不遵循上述模式，将手动覆盖此名称。 在这些情况下，可以使用[`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute)来控制生成绑定的方式。 
getter 或 setter 上的属性，例如：

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

然后，将绑定 "isMenuVisible" 和 "setMenuVisible："。 或者，可以使用以下语法来绑定属性：

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

其中，getter 和 setter 在 `name` 中显式定义为，并 `setName` 绑定。

除了使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)支持静态属性以外，还可以使用[`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)来修饰线程静态属性，例如：

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

与方法允许使用[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)标记某些参数一样，你可以应用[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
如果为属性，则指示 null 是属性的有效值，例如：

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

也可以直接在 setter 上指定[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)参数：

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>绑定自定义控件的注意事项

为自定义控件设置绑定时，应考虑以下注意事项：

1. **绑定属性必须是静态**的-定义属性的绑定时，必须使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)特性。
2. **属性名称必须完全匹配**-用于绑定属性的名称必须与自定义控件中的属性的名称完全匹配。
3. **属性类型必须完全匹配**-用于绑定属性的变量类型必须与自定义控件中的属性的类型完全匹配。
4. 将永远不会命中断点和放置在属性的 getter 或 setter 方法中的**getter/setter**断点。
5. **观察回调**-您将需要使用观察回调来通知自定义控件的属性值的更改。

如果未遵守以上列出的任何警告，可能会导致绑定在运行时以静默方式失败。

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>目标-C 可变模式和属性

目标-C 框架使用一个使用情况，其中某些类对于可变子类是不可变的。 例如 `NSString` 是不可变版本，而 `NSMutableString` 是允许变化的子类。

在这些类中，通常会看到不可变的基类包含带有 getter 的属性，但没有 setter。 以及用于引入 setter 的可变版本。 由于这并不是真正可行C#的，因此我们必须将此方法映射到可使用C#的方法。

映射到C#的方式是在基类上同时添加 getter 和 setter，但使用[`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)来标记资源库
属性中。

然后，在可变子类上，使用[`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
属性上的属性，以确保属性确实会重写父级的行为。

示例：

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>绑定构造函数

`btouch-native` 工具会自动在类中生成 fours 构造函数，对于给定的类 `Foo`，它将生成：

- `Foo ()`：默认构造函数（映射到目标-C 的 "init" 构造函数）
- `Foo (NSCoder)`：在对笔尖文件进行反序列化期间使用的构造函数（映射到目标-C 的 "initWithCoder：" 构造函数）。
- `Foo (IntPtr handle)`：基于句柄的创建的构造函数，在运行时需要从非托管对象公开托管对象时，运行时将调用此构造函数。
- `Foo (NSEmptyFlag)`：派生类使用此方法来防止双重初始化。

对于您定义的构造函数，它们需要在接口定义中使用以下签名进行声明：它们必须返回 `IntPtr` 值并且方法的名称应为构造函数。 例如，若要绑定 `initWithFrame:` 构造函数，请使用以下方法：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>绑定协议

如 API 设计文档中所述，在[讨论模型和协议](~/ios/internals/api-design/index.md#models)部分，Xamarin 将 ansi-c 协议映射到已使用[`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)进行标记的类中。
属性中。 这通常在实现目标 C 委托类时使用。

常规绑定类与委托类之间的主要区别在于委托类可能具有一个或多个可选方法。

例如，请考虑 `UIKit` 类 `UIAccelerometerDelegate`，这就是它在 Xamarin 中的绑定方式：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

由于这是 `UIAccelerometerDelegate` 定义中的可选方法，因此不需要执行任何其他操作。 但如果协议上有必需的方法，则应将[`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
方法的特性。 这会强制该实现的用户实际提供该方法的主体。

通常，协议用于响应消息的类。 此操作通常在目标-C 中完成，方法是向 "委托" 属性分配一个响应协议中的方法的对象的实例。

Xamarin 中的约定是支持目标-C 松耦合样式，其中任何 `NSObject` 的实例都可以分配给委托，还可以公开其强类型版本。 出于此原因，我们通常同时提供强类型的 `Delegate` 属性和松散类型化的 `WeakDelegate`。 我们通常会将松类型版本绑定到[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)，并使用[`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)特性来提供强类型版本。

这会显示我们如何绑定 `UIAccelerometer` 类：

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Monotouch.dialog 7.0 中的新增项**

从 Monotouch.dialog 7.0 开始，已合并了新的和改进的协议绑定功能。  这一新的支持使使用惯例的目标更简单，以便在给定类中采用一个或多个协议。

对于目标为 C 的每个协议定义 `MyProtocol`，现在有一个 `IMyProtocol` 接口，其中列出了协议所需的所有方法以及提供所有可选方法的扩展类。  以上与 Xamarin Studio 编辑器中的新支持结合使用，使开发人员可以实现协议方法，而不必使用前面抽象模型类的单独子类。

任何包含[`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)属性的定义实际上将生成三个支持类，这些类可大大提高你使用协议的方式：

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

**类实现**提供了一个完整的抽象类，您可以重写的各个方法，并获得完整的类型安全。  但由于C#不支持多个继承，因此在某些情况下，你可能需要具有不同的基类，但你仍需要实现一个接口，即

生成的**接口定义**。  它是一个接口，它具有协议中所有必需的方法。  这样，开发人员就可以实现协议来仅实现接口。  运行时将自动将类型注册为采用协议。

请注意，接口仅列出所需的方法，并公开可选方法。  这意味着采用协议的类将为所需的方法获取完整的类型检查，但必须使用弱类型化（手动使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
属性和匹配的签名）。

为了便于使用使用协议的 API，绑定工具还将生成一个扩展方法类，该类公开所有可选方法。  这意味着只要你使用 API，就能将协议视为具有所有方法。

如果要在 API 中使用协议定义，则需要在 API 定义中编写主干空接口。  如果要在 API 中使用 MyProtocol，则需要执行以下操作：

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

需要使用以上方法，因为在绑定时，`IMyProtocol` 不存在，这就是需要提供空接口的原因。

#### <a name="adopting-protocol-generated-interfaces"></a>采用协议生成的接口

每次实现为协议生成的接口之一时，如下所示：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

接口方法的实现会自动以适当的名称导出，因此它等效于：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

接口是隐式实现还是显式实现，这并不重要。

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>绑定类扩展

在目标-C 中，可以扩展具有新方法的类，与在中扩展C#方法中相似。 当其中一种方法存在时，可以使用[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
用于将方法标记为目标 C 消息的接收方的属性。

例如，在 Xamarin 中，我们将 `UIKit` 作为方法 `NSStringDrawingExtensions`中的方法导入时，绑定到 `NSString` 上定义的扩展方法，如下所示：

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>绑定目标-C 参数列表

目标-C 支持可变参数参数。 例如：

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

若要从C#调用此方法，需要创建如下所示的签名：

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

这会将方法声明为内部方法，并隐藏用户的上述 API，但将其公开到库。 然后，可以编写如下所示的方法：

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>绑定字段

有时，您需要访问在库中声明的公共字段。

通常，这些字段包含必须引用的字符串或整数值。 它们通常用作表示特定通知的字符串，以及字典中的键。

若要绑定字段，请将属性添加到接口定义文件，并使用[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)特性修饰属性。 此属性采用一个参数：要查找的符号的 C 名称。 例如：

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

如果要包装静态类中不是从 `NSObject`派生的各个字段，可以使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
类上的特性，如下所示：

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

上面将生成一个不是从 `NSObject` 派生的 `LonelyClass`，它将包含一个指向 `NSSomeEventNotification`
的绑定，`NSString` 公开为 `NSString`。

[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)特性可应用于以下数据类型：

- `NSString` 引用（仅只读属性）
- `NSArray` 引用（仅只读属性）
- 32位整数（`System.Int32`）
- 64位整数（`System.Int64`）
- 32位浮点数（`System.Single`）
- 64位浮点数（`System.Double`）
- `System.Drawing.SizeF`
- `CGSize`

除了本机字段名称外，还可以通过传递库名称来指定字段所在的库的名称：

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

如果是静态链接，则没有要绑定到的库，因此需要使用 `__Internal` 名称：

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>绑定枚举

您可以直接在绑定文件中添加 `enum`，以便更轻松地在 API 定义中使用它们，而无需使用不同的源文件（需要在绑定和最终项目中进行编译）。

示例：

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

还可以创建自己的枚举来替换 `NSString` 常量。 在这种情况下，生成器将**自动**创建方法来转换枚举值和 NSString 常量。

示例：

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

在上面的示例中，你可以决定使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性修饰 `void Perform (NSString mode);`。 这将**隐藏**绑定使用者的基于常量的 API。

但这会限制类型为子类，因为更好 API 替代方法使用[`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)特性。 这些生成的方法不 `virtual`，也就是说，您将无法重写它们，这可能是一个不错的选择。

一种替代方法是将原始的、基于 `NSString`的定义标记为 `[Protected]`。 这将允许在需要时进行子类工作，并且 wrap'ed 版本仍将正常工作并调用重写的方法。

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>将 `NSValue`、`NSNumber`和 `NSString` 绑定到更好的类型

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)特性允许将 `NSNumber`、`NSValue` 和 `NSString`（枚举）绑定到更准确C#的类型。 特性可用于通过本机 API 创建更好、更准确的 .NET API。

可以通过[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)修饰方法（返回值）、参数和属性。 唯一的限制是，成员**不**能位于[`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
或[`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)接口。

例如：

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

输出：

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

在内部，我们将 `bool?` <-> `NSNumber` 并 `CGRect` <-> `NSValue` 转换。

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)还支持 `NSNumber` `NSValue` 和 `NSString`（枚举）的数组。

例如：

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

输出：

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` 是 `NSString` 支持的枚举，我们将提取正确的 `NSString` 值并处理类型转换。

请参阅[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)文档，查看支持的转换类型。

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>绑定通知

通知是发送到 `NSNotificationCenter.DefaultCenter` 的消息，用作将消息从应用程序的一个部分广播到另一个部分的机制。 开发人员通常使用[NSNotificationCenter](xref:Foundation.NSNotificationCenter)的[AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification}))方法订阅通知。 当应用程序将消息发送到通知中心时，它通常包含存储在[NSNotification](xref:Foundation.NSNotification.UserInfo)字典中的负载。 此字典是弱类型的，从该字典中获取信息并不容易出错，因为用户通常需要在文档中阅读哪些密钥在字典上可用以及可以存储在字典中的值的类型。 键的存在有时也用作布尔值。

Xamarin 绑定生成器为开发人员提供了对绑定通知的支持。 为此，请将[`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
已使用[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)标记的属性的特性
属性（可以是公共的或私有的）。

此特性可用于不带有效负载的通知的参数，也可以指定在 API 定义中引用另一个接口的 `System.Type`，通常名称以 "EventArgs" 结尾。 生成器会将接口转换为子类 `EventArgs` 的类，并将包含列出的所有属性。 应在 EventArgs 类中使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性来列出用于查找目标 C 字典以获取该值的键的名称。

例如：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上面的代码将使用以下方法生成嵌套类 `MyClass.Notifications`：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

然后，你的代码用户可以使用类似如下的代码轻松订阅发布到[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)的通知：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

从 `ObserveDidStart` 返回的值可用于轻松地停止接收通知，如下所示：

```csharp
token.Dispose ();
```

或者，可以调用[NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject))并传递令牌。 如果通知中包含参数，则应指定帮助器 `EventArgs` 接口，如下所示：

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

上面将生成一个 `MyScreenChangedEventArgs` 类，该类具有 `ScreenX` 和 `ScreenY` 属性，这些属性分别使用关键字 "ScreenXKey" 和 "ScreenYKey" 提取[NSNotification](xref:Foundation.NSNotification.UserInfo)字典中的数据，并应用正确的转换。 `[ProbePresence]` 特性用于生成器，以便在 `UserInfo`中设置键时进行探测，而不是尝试提取值。 这适用于出现密钥的情况是值（通常用于布尔值）。

这允许你编写如下代码：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>绑定类别

类别是一个目标 C 机制，用于扩展类中可用的一组方法和属性。   在实践中，当在中链接特定的框架时（例如 `UIKit`），它们用于扩展基类的功能（例如 `NSObject`），因此，仅当新框架在中链接时，它们才可用。   在某些其他情况下，它们用于按功能组织类中的功能。   它们在本质上与C#扩展方法相似。这是一种类别在目标-C 中的外观：

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

如果在库中找到，则使用 `makeBackgroundRed`方法扩展 `UIView` 的实例。

若要绑定这些属性，可以在接口定义上使用[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)特性。  使用[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)时
特性， [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)的含义 
用于指定要扩展的基类的特性更改将被指定为要扩展的类型。

下面演示了如何绑定 `UIView` 扩展并将其转换为C#扩展方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

以上将创建一个 `MyUIViewExtension` 包含 `MakeBackgroundRed` 扩展方法的类。  这意味着，你现在可以在任何 `UIView` 子类上调用 "MakeBackgroundRed"，为你提供的功能与在目标 C 上所获得的功能相同。 在某些其他情况下，类别用于扩展系统类，但只是为了进行装饰而对功能进行组织。  类似于下面这样：

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

尽管可以使用[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
特性对于此修饰样式的声明，您可以将它们全部添加到类定义中。  这两种方法都能实现相同的目的：

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

在这些情况下，合并类别只是较短：

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>绑定块

块是 Apple 引入的一种新构造，使C#匿名方法的功能等同于目标-C。 例如，`NSSet` 类现在公开此方法：

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

上述说明声明了一个名为 `enumerateObjectsUsingBlock:` 的方法，该方法采用一个名为 `block`的参数。 此块类似于C#匿名方法，因为它支持捕获当前环境（"this" 指针、对局部变量和参数的访问）。 中的上述方法 `NSSet` 调用带有两个参数的块： `NSObject` （`id obj` 部分）和指向布尔（`BOOL *stop`）部分的指针。

若要使用 btouch 绑定此类 API，需要首先将块类型签名声明为C#委托，然后从 API 入口点引用它，如下所示：

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

现在，你的代码可以从C#以下代码调用函数：

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

如果愿意，还可以使用 lambda：

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>异步方法

绑定生成器可以将某个类的方法转换为异步友好方法（返回任务或任务&lt;T&gt;）的方法。

您可以使用[`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
返回 void 的方法的属性，其最后一个参数是回调。  将此应用于方法时，绑定生成器将生成该方法的版本，并 `Async`后缀。  如果回调不使用参数，则返回值将为 `Task`，如果回调采用参数，则结果将为 `Task<T>`。  如果回调采用多个参数，则应设置 `ResultType` 或 `ResultTypeName`，以指定将包含所有属性的生成类型所需的名称。

示例：

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

上面的代码将同时生成 Assembly.loadfile 方法和：

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>为弱 NSDictionary 参数呈现强类型

在目标-C API 的多个位置中，参数是使用特定的键和值作为弱类型化 `NSDictionary` Api 传递的，但这些参数容易出错（你可以传递无效的密钥，而不会出现警告; 你可以传递无效的值，并且不发出警告），并且在需要多次访问文档以查找可能的密钥名称和值时，可以使用这些参数

解决方案是提供强类型的版本，该版本提供 API 的强类型版本，并在后台映射各种基础键和值。

例如，如果目标为 C 的 API 接受了一个 `NSDictionary`，并将其记录为采用密钥 `XyzVolumeKey` 这会将具有从0.0 到1.0 的卷值的 `NSNumber` 和一个采用字符串的 `XyzCaptionKey`，你会希望用户具有如下所示的理想 API：

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume` 属性被定义为可以为 null 的 float，因为在目标-C 中的约定不要求这些字典具有值，因此，在某些情况下可能不会设置此值。

为此，您需要执行以下操作：

- 创建一个强类型类，该子类[DictionaryContainer](xref:Foundation.DictionaryContainer)并为每个属性提供各种 getter 和 setter。
- 声明 `NSDictionary` 采用新的强类型版本的方法的重载。

可以手动创建强类型类，也可以使用生成器来完成工作。  首先，我们将探讨如何手动执行此操作，以便您了解会发生什么情况，然后再执行自动方法。

需要为此创建支持文件，不会将其放入协定 API。  这就是创建 XyzOptions 类时必须编写的内容：

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

然后，应提供一个包装方法，该方法在低级别 API 的基础上显示高级别 API。

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果无需覆盖 API，可以使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)安全地隐藏基于 NSDICTIONARY 的 api
属性中。

如您所见，我们使用[`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
用于显示新 API 入口点的属性，并使用强类型的 `XyzOptions` 类进行呈现。  包装方法还允许传递 null。

现在，我们没有提到的一件事是 `XyzOptionsKeys` 值来自的位置。  通常，会按如下所示对某个 API 在 `XyzOptionsKeys`静态类中的表面进行分组：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

让我们看一下自动支持如何创建这些强类型的字典。  这样可以避免出现大量的样本，并且可以直接在 API 协定中定义字典，而不是使用外部文件。

若要创建强类型的字典，请在 API 中引入一个接口，并使用[StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)属性对其进行修饰。  这会告知生成器应创建一个与将派生自 `DictionaryContainer` 的接口同名的类，并为其提供强类型访问器。

[`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)属性采用一个参数，该参数是包含字典键的静态类的名称。  然后，接口的每个属性都将成为强类型化访问器。  默认情况下，代码将在静态类中使用后缀为 "Key" 的属性的名称来创建访问器。

这意味着，创建强类型访问器不再需要外部文件，也不需要为每个属性手动创建 getter 和 setter，也不必手动查找密钥。

整个绑定如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果需要在 `XyzOption` 成员中引用不同的字段（这不是后缀 `Key`的属性的名称），则可以使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)修饰属性 
要使用的名称的特性。

<a name="Type_mappings" />

## <a name="type-mappings"></a>类型映射

本部分介绍如何将目标 C 类型映射到C#类型。

<a name="Simple_Types" />

### <a name="simple-types"></a>简单类型

下表显示了应如何将目标 C 和 CocoaTouch 世界的类型映射到 Xamarin world：

|目标-C 类型名称|Xamarin Unified API 类型|
|---|---|
|`BOOL`、`GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` （有关[绑定 NSString 的详细信息](~/ios/internals/api-design/nsstring.md)）|`string`|
|`char *`|`string` （另请参阅： [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)）|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`、`GLfloat`|`nfloat`|
|CoreFoundation 类型（`CF*`）|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|基础类型（`NS*`）|`Foundation.NS*`|
|`id`|`Foundation`。`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>数组

Xamarin 运行时自动处理将数组转换C#为 `NSArrays` 和执行转换的过程，例如，返回 `UIViews`的虚目标 C 方法 `NSArray`：

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

绑定如下：

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

其思路是使用强类型化C#的数组，因为这将允许 IDE 使用实际类型提供适当的代码完成，而无需强制用户推测，或查找文档以找出数组中包含的对象的实际类型。

如果无法跟踪数组中包含的实际派生程度最高的类型，则可以使用 `NSObject []` 作为返回值。

<a name="Selectors" />

### <a name="selectors"></a>选择器

选择器在目标 C API 上显示为 `SEL`的特殊类型。 绑定选择器时，会将该类型映射到 `ObjCRuntime.Selector`。  通常，选择器会在具有对象、目标对象和选择器的 API 中公开，以便在目标对象中调用。 同时提供这两种方法基本上对应C#于委托：封装要调用的方法的对象以及用于调用方法的对象。

绑定如下：

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

这就是该方法通常在应用程序中的使用方式：

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

为了使绑定更好C#开发人员，通常会提供一个方法，该方法采用 `NSAction` 参数，该参数允许C#使用委托和 lambda 而不是 `Target+Selector`。 为此，通常需要使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)来标记 `SetTarget` 方法
特性，然后将公开新的帮助器方法，如下所示：

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

现在，可以按如下所示编写用户代码：

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>字符串

绑定采用 `NSString`的方法时，可以将其替换为返回类型和参数上C#的字符串类型。

当字符串用作标记时，唯一的情况是你可能想要直接使用 `NSString`。 有关字符串和 `NSString`的详细信息，请参阅[NSString 文档上的 API 设计](~/ios/internals/api-design/nsstring.md)。

在某些极少数情况下，API 可能会公开类似于 C 的字符串（`char *`）而不是目标 C 字符串（`NSString *`）。 在这些情况下，你可以用[`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)注释参数
属性中。

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out/ref 参数

某些 Api 在其参数中返回值，或按引用传递参数。

通常，签名如下所示：

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

第一个示例演示一个常见的目标 C 用于返回错误代码，传递指向 `NSError` 指针的指针，并在返回值时设置。   第二种方法显示目标 C 方法如何获取对象并修改其内容。   这将是按引用传递，而不是纯输出值。

绑定如下所示：

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>内存管理属性

当使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性并且要传递的数据将由被调用方法保留时，可以通过将其作为第二个参数传递来指定参数语义，例如：

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

以上会将值标记为具有 "保留" 语义。 可用的语义包括：

- 分配
- 复制
- 保留

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>样式准则

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>使用 [Internal]

您可以使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
用于隐藏公共 API 中的方法的属性。 如果公开的 API 太低，并且你希望在基于此方法的单独文件中提供高级实现，则可能需要执行此操作。

当你在绑定生成器中遇到限制时，还可以使用此方法，例如，某些高级方案可能会公开未绑定的类型，并且你想要以自己的方式绑定这些类型，并且你想要以自己的方式自行包装这些类型。

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>事件处理程序和回调

目标-C 类通常通过发送委托类（目标-C 委托）上的消息来广播通知或请求信息。

此模型在完全受支持的情况下和由 Xamarin 呈现时可能会很繁琐。 Xamarin 公开了可在C#这些情况下使用的类中的事件模式和方法回调系统。 这允许运行如下代码：

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

绑定生成器能够减少将目标 C 模式映射到C#模式所需的输入量。

从 Xamarin 1.4 开始，还可以指示生成器生成特定目标-C 委托的绑定，并将该委托公开为C#主机类型上的事件和属性。

此过程涉及两个类，即当前发出事件的宿主类，然后将这些类发送到 `Delegate` 或 `WeakDelegate` 以及实际的委托类中。

考虑以下设置：

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

若要包装类，您必须：

- 在主机类中，将添加到[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   声明作为委托的类型以及你公开的C#名称。 在上面的示例中，分别 `typeof (MyClassDelegate)` 和 `WeakDelegate`。
- 在您的委托类中，对于每个具有两个以上参数的方法，您需要指定要用于自动生成的 EventArgs 类的类型。

绑定生成器并不局限于只包装单个事件目标，某些目标 C 类可能会将消息发送到多个委托，因此您将需要提供支持此设置的数组。 大多数设置不需要它，但生成器已准备就绪，可支持这些情况。

生成的代码将为：

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

`EventArgs` 用于指定要生成的 `EventArgs` 类的名称。 应为每个签名使用一个（在此示例中，`EventArgs` 将包含 nint 类型的 `With` 属性）。

在上面的定义中，生成器将在生成的 MyClass 中生成以下事件：

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

现在，可以使用如下所示的代码：

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

回调与事件调用相同，不同之处在于，不是具有多个潜在的订户（例如，多个方法可以挂钩到 `Clicked` 事件或 `DownloadFinished` 事件）回调只能有一个订阅服务器。

此过程是相同的，唯一的区别在于，唯一的 `EventArgs` 区别在于，该 EventArgs 实际上用于命名生成的C#委托名称。

如果委托类中的方法返回值，则绑定生成器会将此映射到父类中的委托方法，而不是事件。 在这些情况下，如果用户不挂钩到委托，则需要提供方法应返回的默认值。 使用[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
或[`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)特性。

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)会将返回值硬编码，而[`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
用于指定将返回的输入参数。

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>枚举和基类型

你还可以引用 btouch 接口定义系统不直接支持的枚举或基类型。 为此，请将枚举和核心类型置于单独的文件中，并将其作为提供给 btouch 的一个额外文件的一部分包括在内。

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>链接依赖项

如果要绑定不属于应用程序的 Api，则需要确保可执行文件与这些库相关联。

你需要通知 Xamarin iOS 如何链接库，这可以通过以下方式完成：更改生成配置以调用 `mtouch` 命令，其中包含一些额外的生成参数，这些自变量指定如何使用 "-gcc_flags" 选项链接到新库，后面跟有带引号的字符串，其中包含程序所需的所有额外库，如下所示：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

上面的示例将 `libMyLibrary.a`、`libSystemLibrary.dylib` 和 `CFNetwork` framework 库链接到您的最终可执行文件中。

或者，你可以利用程序集级别的[`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，你可以将其嵌入到协定文件中（如 `AssemblyInfo.cs`）。
当你使用[`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)时，你将需要在进行绑定时使用本机库，因为这会将本机库嵌入应用程序。 例如：

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

你可能会想知道，为什么需要 `-force_load` 命令，原因是-ObjC 标志尽管它在中编译代码，但它不会保留支持类别所需的元数据（链接器/编译器死代码去除它），而你需要在运行时进行 Xamarin。

<a name="Assisted_References" />

## <a name="assisted-references"></a>辅助参考

某些暂时性对象（如操作表和警报框）对于跟踪开发人员来说是一件很麻烦的工作，绑定生成器可在此处提供帮助。

例如，如果你有一个类，其中显示了一条消息，然后生成了 `Done` 事件，则处理此事件的传统方法是：

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

在上述方案中，开发人员需要保持对对象的引用，并在自己的中泄漏或积极清除对 box 的引用。  绑定代码时，生成器支持跟踪引用，并在调用特殊方法时清除它，然后，上述代码将变为：

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

请注意，不需要在实例中保留变量，因为它与局部变量一起使用，并且在对象停止时不需要清除引用。

若要利用这一点，你的类应在[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)声明中设置 Events 属性，同时将 `KeepUntilRef` 变量设置为在对象完成其工作时调用的方法的名称，如下所示：

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>继承协议

自 Xamarin 版本3.2 开始，我们支持从已标记为[`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)属性的协议继承。 这在某些 API 模式下非常有用，例如在 `MKOverlay` 协议从 `MKAnnotation` 协议继承的 `MapKit` 中，并被多个继承自 `NSObject`的类采用。

在过去，我们需要将协议复制到每个实现中，但在这种情况下，我们可以让 `MKShape` 类继承自 `MKOverlay` 协议，它将自动生成所有必需的方法。

## <a name="related-links"></a>相关链接

- [绑定示例](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
