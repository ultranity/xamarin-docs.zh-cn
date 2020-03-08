---
title: Xamarin 中的数据绑定和键/值编码
description: 本文介绍如何使用键/值编码和键-值观察，以允许数据绑定到 Xcode 的 Interface Builder 中的 UI 元素。
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 81a1f63078a5f7a2a70f731d1790f85f4283d22f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78919144"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Xamarin 中的数据绑定和键/值编码

_本文介绍如何使用键/值编码和键-值观察，以允许数据绑定到 Xcode 的 Interface Builder 中的 UI 元素。_

## <a name="overview"></a>概述

在 Xamarin Mac C#应用程序中使用和 .net 时，可以访问在*Xcode 和* *中工作*的开发人员所使用的相同的键/值编码和数据绑定技术。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的_Interface Builder_与 UI 元素进行数据绑定，而不是编写代码。

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 您还可以从您的前端用户界面（_模型-视图-控制器_）进一步分离您的备份数据（_数据模型_），从而更易于维护，更灵活的应用程序设计。

[![正在运行的应用的示例](databinding-images/intro01.png "正在运行的应用的示例")](databinding-images/intro01-large.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用键/值编码和数据绑定的基本知识。 强烈建议您先完成[Hello，Mac](~/mac/get-started/hello-mac.md)一文，特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)及[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 " C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md) " 部分，它解释了用于将类连接到目标 c 对象和 UI 元素的 `Register` 和 `Export` 属性。

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>什么是键-值编码

键-值编码（KVC）是一种用于间接访问对象属性的机制，使用键（特殊格式的字符串）来标识属性，而不是通过实例变量或访问器方法（`get/set`）来访问这些属性。 通过在 Xamarin 应用程序中实现符合键值的代码访问器，你可以访问其他 macOS （以前称为 OS X）功能，例如键-值观察（KVO）、数据绑定、核心数据、Cocoa 绑定和 scriptability。

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 您还可以从您的前端用户界面（_模型-视图-控制器_）进一步分离您的备份数据（_数据模型_），从而更易于维护，更灵活的应用程序设计。

例如，让我们看看 KVC 兼容对象的以下类定义：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

首先，`[Register("PersonModel")]` 属性注册类并将其公开给目标-C。 然后，类需要从 `NSObject` （或继承自 `NSObject`的子类）继承，这会添加几个允许类 KVC 兼容的基方法。 接下来，`[Export("Name")]` 特性公开 `Name` 属性，并定义稍后用于通过 KVC 和 KVO 技术访问属性的键值。

最后，若要能够以键值方式观察到对属性值的更改，访问器必须在 `WillChangeValue` 和 `DidChangeValue` 方法调用中包装其值的更改（指定与 `Export` 特性相同的键）。  例如：

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

此步骤对于 Xcode 的 Interface Builder 中的数据绑定_非常_重要（如本文稍后将介绍）。

有关详细信息，请参阅 Apple 的[键-值编码编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)。

### <a name="keys-and-key-paths"></a>密钥和密钥路径

_键_是标识对象的特定属性的字符串。 通常，键对应于键值编码符合对象中的访问器方法的名称。 密钥必须使用 ASCII 编码，通常以小写字母开头，并且不能包含空格。 因此，在上面的示例中，`Name` 将是 `PersonModel` 类的 `Name` 属性的键值。 键及其公开的属性的名称不必相同，但是在大多数情况下，它们都是相同的。

_键路径_是一个用句点分隔的键组成的字符串，用于指定要遍历的对象属性的层次结构。 序列中第一个键的属性相对于接收方，并且每个后续键都是相对于上一个属性的值计算的。 与使用点表示法来遍历C#类中的对象及其属性的方式相同。

例如，如果扩展了 `PersonModel` 类并添加了 `Child` 属性：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

子级名称的密钥路径将为 `self.Child.Name` 或只 `Child.Name` （基于如何使用键值）。

### <a name="getting-values-using-key-value-coding"></a>使用键/值编码获取值

`ValueForKey` 方法返回指定键的值（作为 `NSString`），相对于接收请求的 KVC 类的实例。 例如，如果 `Person` 是上面定义的 `PersonModel` 类的实例：

```csharp
// Read value
var name = Person.ValueForKey (new NSString("Name"));
```

这会返回 `PersonModel`的该实例的 `Name` 属性的值。

### <a name="setting-values-using-key-value-coding"></a>使用键值编码设置值

同样，`SetValueForKey` 会相对于接收请求的 KVC 类的实例，设置指定键的值（作为 `NSString`）。 同样，使用 `PersonModel` 类的实例，如下所示：

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

会将 `Name` 属性的值更改为 `Jane Doe`。

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>观察值更改

使用键值感知（KVO），可以将观察者附加到 KVC 相容类的特定键，并在每次修改该键的值时收到通知（使用 KVC 技术或直接访问代码中C#的给定属性）。 例如：

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

现在，每当 `PersonModel` 类的 `Person` 实例的 `Name` 属性被修改时，新值就会写出到控制台。

有关详细信息，请参阅 Apple[对关键值观察编程指南的介绍](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)。

## <a name="data-binding"></a>数据绑定

以下各节将介绍如何使用键/值代码和键值一致的类将数据绑定到 Xcode 的 Interface Builder 中的 UI 元素，而不是使用C#代码读取和写入值。 通过这种方式，您可以将_数据模型_与用于显示它们的视图进行分隔，使 Xamarin 应用程序更灵活、更易于维护。 您还可以极大地减少需要写入的代码量。

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>定义数据模型

在 Interface Builder 中的 UI 元素进行数据绑定之前，你必须在 Xamarin. Mac 应用程序中定义 KVC/KVO 兼容的类，使其充当绑定的_数据模型_。 数据模型提供将在用户界面中显示的所有数据，并在运行应用程序时接收用户在 UI 中所做数据的任何修改。

例如，如果您正在编写一组员工管理的应用程序，则可以使用以下类定义数据模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

此类的大部分功能都在上面的 "[关键值编码](#What_is_Key-Value_Coding)" 一节中介绍。 不过，让我们来看看一些具体的元素，以及一些为允许此类充当**数组控制器**和**树控制器**的数据模型（稍后将用于数据绑定**树视图**、**大纲视图**和**集合视图**）的一些内容。

首先，因为员工可能是经理，所以我们使用了 `NSArray` （具体来说是 `NSMutableArray` 以便可以修改值），以允许他们所管理的员工附加到他们：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

此处需要注意两点：

1. 我们使用了 `NSMutableArray` 而不是标准C#的数组或集合，因为这是将数据绑定到**表视图**、**大纲视图**和**集合**等 AppKit 控件的要求。
2. 我们公开了员工数组，方法是将其强制转换为用于数据绑定的 `NSArray`， C#并将其格式化名称（`People`）更改为数据绑定所需的名称，`personModelArray` 格式为 **{class_name} 数组**（请注意，第一个字符已被小写）。

接下来，我们需要添加一些专门的名称公共方法，以支持**数组控制器**和**树控制器**：

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

它们允许控制器请求和修改它们显示的数据。 与上面公开的 `NSArray` 相似，它们具有非常具体的命名约定（与典型C#的命名约定不同）：

- `addObject:`-将对象添加到数组中。
- `insertObject:in{class_name}ArrayAtIndex:`-其中，`{class_name}` 是你的类的名称。 此方法将对象插入到数组中的指定索引处。
- `removeObjectFrom{class_name}ArrayAtIndex:`-其中，`{class_name}` 是你的类的名称。 此方法删除数组中给定索引处的对象。
- `set{class_name}Array:`-其中，`{class_name}` 是你的类的名称。 此方法允许您将现有的执行替换为新的。

在这些方法中，我们已将对数组的更改包装到 `WillChangeValue`，并 `DidChangeValue` 消息来 KVO 符合性。

最后，由于 `Icon` 属性依赖于 `isManager` 属性的值，对 `isManager` 属性的更改可能不会反映在数据绑定 UI 元素（在 KVO 期间）的 `Icon` 中：

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
```

若要更正此错误，请使用以下代码：

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

请注意，除自己的密钥外，`isManager` 访问器还会发送 `Icon` 密钥的 `WillChangeValue` 和 `DidChangeValue` 消息，以便它也会看到更改。

本文的其余部分将使用 `PersonModel` 数据模型。

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>简单数据绑定

定义数据模型后，让我们看一下 Xcode 的 Interface Builder 中的数据绑定的简单示例。 例如，让我们向 Xamarin Mac 应用程序添加一个窗体，该窗体可用于编辑上面定义的 `PersonModel`。 我们将添加几个文本字段和一个复选框，以显示和编辑模型的属性。

首先，让我们将新的**视图控制器**添加到 Interface Builder 中的**主情节提要**文件，并将其类命名 `SimpleViewController`：

[![添加新的视图控制器](databinding-images/simple01.png "添加新的视图控制器")](databinding-images/simple01-large.png#lightbox)

接下来，返回到 Visual Studio for Mac，编辑**SimpleViewController.cs**文件（已自动添加到我们的项目），并公开要将窗体数据绑定到的 `PersonModel` 的实例。 添加以下代码：

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

接下来，在加载视图时，让我们创建一个 `PersonModel` 的实例，然后使用以下代码填充它：

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

现在，我们需要创建窗体，双击要打开的**主情节提要**文件，以便在 Interface Builder 中进行编辑。 布局窗体，如下所示：

[![在 Xcode 中编辑情节提要](databinding-images/simple02.png "在 Xcode 中编辑情节提要")](databinding-images/simple02-large.png#lightbox)

若要将窗体数据绑定到通过 `Person` 项公开的 `PersonModel`，请执行以下操作：

1. 选择 "**员工姓名**" 文本字段并切换到**绑定检查器**。
2. 选中 "**绑定到**" 框，然后从下拉列表中选择 "**简单视图控制器**"。 接下来，输入**密钥路径**`self.Person.Name`：

    [![输入密钥路径](databinding-images/simple03.png "输入密钥路径")](databinding-images/simple03-large.png#lightbox)
3. 选择 "**职业**" 文本字段并选中 "**绑定到**" 框，然后从下拉列表中选择 "**简单视图控制器**"。 接下来，输入**密钥路径**`self.Person.Occupation`：

    [![输入密钥路径](databinding-images/simple04.png "输入密钥路径")](databinding-images/simple04-large.png#lightbox)
4. 选择 "**员工是经理**" 复选框并选中 "**绑定到**" 框，然后从下拉列表中选择 "**简单视图控制器**"。 接下来，输入**密钥路径**`self.Person.isManager`：

    [![输入密钥路径](databinding-images/simple05.png "输入密钥路径")](databinding-images/simple05-large.png#lightbox)
5. 选择 "**管理的员工数**" 文本字段，并选中 "**绑定到**" 框，然后从下拉列表中选择 "**简单视图控制器**"。 接下来，输入**密钥路径**`self.Person.NumberOfEmployees`：

    [![输入密钥路径](databinding-images/simple06.png "输入密钥路径")](databinding-images/simple06-large.png#lightbox)
6. 如果员工不是经理，我们需要隐藏 "托管标签" 和 "文本字段" 的 "员工数"。
7. 选择 "**管理的员工数**" 标签，展开**隐藏**的 Turndown 并选中 "**绑定到**" 框，然后从下拉列表中选择 "**简单视图控制器**"。 接下来，输入**密钥路径**`self.Person.isManager`：

    [![输入密钥路径](databinding-images/simple07.png "输入密钥路径")](databinding-images/simple07-large.png#lightbox)
8. 从 "**值变压器**" 下拉列表中选择 `NSNegateBoolean`：

    ![选择 NSNegateBoolean 键转换](databinding-images/simple08.png "选择 NSNegateBoolean 键转换")
9. 这会通知数据绑定，如果 `isManager` 属性的值 `false`，则会隐藏标签。
10. 对于 "**员工管理**的文本" 字段，重复步骤7和8。
11. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

如果运行应用程序，`Person` 属性中的值将自动填充我们的窗体：

[![显示自动填充的窗体](databinding-images/simple09.png "显示自动填充的窗体")](databinding-images/simple09-large.png#lightbox)

用户对窗体所做的任何更改都将写回到视图控制器中的 `Person` 属性。 例如，取消选中**员工是经理**更新 `PersonModel` 的 `Person` 实例，并且会自动隐藏 "**受管理的员工数**" 标签和 "文本" 字段（通过数据绑定）：

[![隐藏非经理的员工数](databinding-images/simple10.png "隐藏非经理的员工数")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>表视图数据绑定

现在我们已经有了数据绑定基础知识，接下来让我们通过使用_数组控制器_和表视图的数据绑定来了解更复杂的数据绑定任务。 有关使用表视图的详细信息，请参阅我们的[表视图](~/mac/user-interface/table-view.md)文档。

首先，让我们将新的**视图控制器**添加到 Interface Builder 中的**主情节提要**文件，并将其类命名 `TableViewController`：

[![添加新的视图控制器](databinding-images/table01.png "添加新的视图控制器")](databinding-images/table01-large.png#lightbox)

接下来，让我们编辑**TableViewController.cs**文件（已自动添加到项目中），并公开 `PersonModel` 类的数组（`NSArray`），我们将把窗体数据绑定到该文件。 添加以下代码：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

就像我们在 "[定义数据模型](#Defining_your_Data_Model)" 一节中的 "`PersonModel`" 类上所做的那样，我们公开了四个特殊命名的公共方法，以便数组控制器和从我们的 `PersonModels`集合中读取和写入数据。

接下来，在加载视图时，需要用以下代码填充数组：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

现在，我们需要创建表视图，双击**主情节提要**文件以将其打开，以便在 Interface Builder 中进行编辑。 布局表的布局如下所示：

[![布局新的表视图](databinding-images/table02.png "布局新的表视图")](databinding-images/table02-large.png#lightbox)

需要添加一个**数组控制器**来向我们的表提供绑定数据，请执行以下操作：

1. 将**数组控制器**从**库检查器**拖到**接口编辑器**：

    ![从库中选择阵列控制器](databinding-images/table03.png "从库中选择阵列控制器")
2. 在**接口层次结构**中选择 "**数组控制器**"，并切换到 "**属性检查器**"：

    [![选择特性检查器](databinding-images/table04.png "选择特性检查器")](databinding-images/table04-large.png#lightbox)
3. 为 "**类名称**" 输入 `PersonModel`，单击**加号**按钮并添加三个键。 `Name`、`Occupation` 和 `isManager`命名它们：

    ![添加所需的密钥路径](databinding-images/table05.png "添加所需的密钥路径")
4. 这会告知数组控制器它所管理的数组，以及应公开的属性（通过键）。
5. 切换到 "**绑定" 检查器**，然后在 "**内容数组**" 下选择 "**绑定到**" 和**表视图控制器**。 输入**模型键路径**`self.personModelArray`：

    ![输入密钥路径](databinding-images/table06.png "输入密钥路径")
6. 这会将数组控制器与我们在视图控制器上公开的 `PersonModels` 数组联系在一起。

现在，我们需要将表视图绑定到数组控制器，请执行以下操作：

1. 选择表视图和**绑定检查器**：

    [![选择绑定检查器](databinding-images/table07.png "选择绑定检查器")](databinding-images/table07-large.png#lightbox)
2. 在**表内容**turndown 下，选择 "**绑定到**" 和 "**数组控制器**"。 输入**控制器密钥**字段 `arrangedObjects`：

    ![定义控制器密钥](databinding-images/table08.png "定义控制器密钥")
3. 选择 " **Employee** " 列下的**表视图单元**。 在 "**绑定" 检查器**的**值**"turndown" 下，选择 "**绑定到**" 和 "**表格单元" 视图**。 输入**模型键路径**`objectValue.Name`：

    [![设置模型键路径](databinding-images/table09.png "设置模型键路径")](databinding-images/table09-large.png#lightbox)
4. `objectValue` 是数组控制器正在管理的数组中的当前 `PersonModel`。
5. 选择 "**职业**" 列下的**表视图单元**。 在 "**绑定" 检查器**的**值**"turndown" 下，选择 "**绑定到**" 和 "**表格单元" 视图**。 输入**模型键路径**`objectValue.Occupation`：

    [![设置模型键路径](databinding-images/table10.png "设置模型键路径")](databinding-images/table10-large.png#lightbox)
6. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

如果运行该应用程序，将用 `PersonModels`数组填充该表：

[![运行应用程序](databinding-images/table11.png "运行应用程序")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>数据绑定大纲视图

针对大纲视图的数据绑定非常类似于对表视图进行绑定。 主要区别在于，我们将使用**树控制器**而不是**数组控制器**将绑定数据提供给大纲视图。 有关使用大纲视图的详细信息，请参阅[大纲视图](~/mac/user-interface/outline-view.md)文档。

首先，让我们将新的**视图控制器**添加到 Interface Builder 中的**主情节提要**文件，并将其类命名 `OutlineViewController`：

[![添加新的视图控制器](databinding-images/outline01.png "添加新的视图控制器")](databinding-images/outline01-large.png#lightbox)

接下来，让我们编辑**OutlineViewController.cs**文件（已自动添加到项目中），并公开 `PersonModel` 类的数组（`NSArray`），我们将把窗体数据绑定到该文件。 添加以下代码：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

就像我们在 "[定义数据模型](#Defining_your_Data_Model)" 一节中的 "`PersonModel`" 类上所做的那样，我们公开了四个特殊命名的公共方法，以便树控制器和从我们的 `PersonModels`集合中读取和写入数据。

接下来，在加载视图时，需要用以下代码填充数组：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

现在，我们需要创建大纲视图，双击要打开的**主情节提要**文件，以便在 Interface Builder 中进行编辑。 布局表的布局如下所示：

[![创建大纲视图](databinding-images/outline02.png "创建大纲视图")](databinding-images/outline02-large.png#lightbox)

我们需要添加一个**树控制器**来向我们的大纲提供绑定数据，请执行以下操作：

1. 从**库检查器**将**树控制器**拖到**接口编辑器**：

    ![从库中选择树控制器](databinding-images/outline03.png "从库中选择树控制器")
2. 选择**接口层次结构**中的**树控制器**并切换到 "**属性检查器**"：

    [![选择特性检查器](databinding-images/outline04.png "选择特性检查器")](databinding-images/outline04-large.png#lightbox)
3. 为 "**类名称**" 输入 `PersonModel`，单击**加号**按钮并添加三个键。 `Name`、`Occupation` 和 `isManager`命名它们：

    ![添加所需的密钥路径](databinding-images/outline05.png "添加所需的密钥路径")
4. 这会告知树控制器它所管理的数组，以及应公开的属性（通过键）。
5. 在 "**树控制器**" 部分下，输入 "**子级**`personModelArray`"，在 "**计数**" 下输入 `NumberOfEmployees`，然后在 "**叶**" 下输入 `isEmployee`：

    ![设置树控制器密钥路径](databinding-images/outline05.png "设置树控制器密钥路径")
6. 这会告知树控制器查找任何子节点的位置、有多少个子节点以及当前节点是否有子节点。
7. 切换到**绑定检查器**，然后在 "**内容数组**" 下选择 "**绑定到**" 和**文件的 "所有者**"。 输入**模型键路径**`self.personModelArray`：

    ![编辑密钥路径](databinding-images/outline06.png "编辑密钥路径")
8. 这会将树控制器与我们在视图控制器上公开的 `PersonModels` 数组联系在一起。

现在，我们需要将大纲视图绑定到树控制器，请执行以下操作：

1. 选择大纲视图，并在**绑定检查器**中选择：

    [![选择绑定检查器](databinding-images/outline07.png "选择绑定检查器")](databinding-images/outline07-large.png#lightbox)
2. 在**大纲视图内容**turndown 下，选择 "**绑定到**" 和 "**树控制器**"。 输入**控制器密钥**字段 `arrangedObjects`：

    ![设置控制器密钥](databinding-images/outline08.png "设置控制器密钥")
3. 选择 " **Employee** " 列下的**表视图单元**。 在 "**绑定" 检查器**的**值**"turndown" 下，选择 "**绑定到**" 和 "**表格单元" 视图**。 输入**模型键路径**`objectValue.Name`：

    [![输入模型键路径](databinding-images/outline09.png "输入模型键路径")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` 是由树控制器管理的数组中的当前 `PersonModel`。
5. 选择 "**职业**" 列下的**表视图单元**。 在 "**绑定" 检查器**的**值**"turndown" 下，选择 "**绑定到**" 和 "**表格单元" 视图**。 输入**模型键路径**`objectValue.Occupation`：

    [![输入模型键路径](databinding-images/outline10.png "输入模型键路径")](databinding-images/outline10-large.png#lightbox)
6. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

如果运行该应用程序，将用 `PersonModels`数组填充轮廓：

[![运行应用程序](databinding-images/outline11.png "运行应用程序")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>集合视图数据绑定

使用集合视图进行数据绑定非常类似于与表视图绑定，因为数组控制器用于为集合提供数据。 由于集合视图没有预设的显示格式，因此提供用户交互反馈和跟踪用户选择时需要执行更多的工作。

> [!IMPORTANT]
> 由于 Xcode 7 和 macOS 10.11 （及更高版本）中的问题，无法在情节提要（storyboard）文件内使用集合视图。 因此，你将需要继续使用 xib 文件来定义你的 Xamarin 应用的集合视图。 有关详细信息，请参阅我们的[集合视图](~/mac/user-interface/collection-view.md)文档。

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`:

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1. **Collection View Item** -  That manages a single instance of an item in the collection.
2. **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>调试本机崩溃

在数据绑定中进行错误可能导致非托管代码中出现_本机崩溃_，并导致 Xamarin 应用程序完全失败，并出现 `SIGABRT` 错误：

[![本机崩溃对话框的示例](databinding-images/debug01.png "本机崩溃对话框的示例")](databinding-images/debug01-large.png#lightbox)

在数据绑定过程中，通常会有四个导致本机崩溃的主要原因：

1. 数据模型未从 `NSObject`的 `NSObject` 或子类继承。
2. 你未使用 `[Export("key-name")]` 特性将属性公开给目标-C。
3. 未将对访问器的值所做的更改换行 `WillChangeValue` 和 `DidChangeValue` 方法调用（指定与 `Export` 特性相同的键）。
4. Interface Builder 的**绑定检查器**中有错误或错误输入的键。

### <a name="decoding-a-crash"></a>解码故障

让我们在数据绑定中导致本机崩溃，以便我们可以介绍如何查找并修复它。 在 Interface Builder 中，让我们将集合视图示例中第一个标签的绑定从 `Name` 更改为 `Title`：

[![编辑绑定键](databinding-images/debug02.png "编辑绑定键")](databinding-images/debug02-large.png#lightbox)

让我们保存更改，切换回 Visual Studio for Mac 以便与 Xcode 同步，然后运行我们的应用程序。 当显示集合视图时，应用程序会暂时崩溃，并显示 `SIGABRT` 错误（如 Visual Studio for Mac 中的**应用程序输出**中所示），因为 `PersonModel` 不会公开包含密钥 `Title`的属性：

[![绑定错误示例](databinding-images/debug03.png "绑定错误示例")](databinding-images/debug03-large.png#lightbox)

在**应用程序输出**中滚动到错误的最顶部，可以看到用于解决此问题的关键：

[![在错误日志中查找问题](databinding-images/debug04.png "在错误日志中查找问题")](databinding-images/debug04-large.png#lightbox)

该行告诉我们，键 `Title` 在要绑定到的对象上不存在。 如果将绑定更改回 Interface Builder 中的 `Name`，保存、同步、重新生成和运行，则应用程序将按预期运行，而不会出现问题。

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用数据绑定和键值编码。 首先，它介绍了如何使用C#键/值编码（KVC）和键-值观察（KVO）向目标-C 公开一个类。 接下来，该示例演示如何使用 KVO 兼容类并将其绑定到 Xcode 的 Interface Builder 中的 UI 元素。 最后，它使用**数组控制器**和**树控制器**展示复杂的数据绑定。

## <a name="related-links"></a>相关链接

- [MacDatabinding 情节提要（示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding Xib （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-xibs)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [标准控件](~/mac/user-interface/standard-controls.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [集合视图](~/mac/user-interface/collection-view.md)
- [键-值编码编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [键-值观察编程指南简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Cocoa 绑定编程主题简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Cocoa 绑定参考简介](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
