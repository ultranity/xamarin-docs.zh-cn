---
title: 了解 Android 多屏显示：深入了解
description: 在这个两部分的指南中，会扩展基本 Phoneword 应用程序（在“Hello，Android”指南中创建）以便处理第二个屏幕。 在此过程中，引入了基本 Android 应用程序构建基块。 对 Android 体系结构的更深入了解包括在内，以帮助你更好地了解 Android 应用程序结构和功能。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E4150036-7760-4023-BD33-B7BDE7B7AF5B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 4acbfe810abefd9a25721ddf59c9f4f197afdf28
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020982"
---
# <a name="hello-android-multiscreen-deep-dive"></a>了解 Android 多屏显示：深入了解

_在这个两部分的指南中，会扩展基本 Phoneword 应用程序（在“Hello，Android”指南中创建）以便处理第二个屏幕。在此过程中，引入了基本 Android 应用程序构建基块。包括对 Android 体系结构的更深入了解，以帮助你更好地了解 Android 应用程序结构和功能。_

在 [Hello，Android 多屏幕快速入门](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md)中，你生成并运行了第一个多屏幕 Xamarin.Android 应用程序。

本指南将探讨更高级的 Android 体系结构。 说明了具有意向  的 Android 导航，并探讨了 Android 硬件导航选项。 剖析了对 Phoneword 应用添加的新功能，同时你会对应用程序与操作系统和其他应用程序之间的关系形成更全面的观点。

## <a name="android-architecture-basics"></a>Android 体系结构基础知识

在 [Hello，Android 深入了解](~/android/get-started/hello-android/hello-android-deepdive.md)中，你了解到 Android 应用程序是独有的程序，因为它们缺少单一入口点。 相反，操作系统（或其他应用程序）可启动应用程序的任何一个已注册活动，这进而会启动应用程序的进程。 此 Android 体系结构深入了解通过介绍 Android 应用程序构建基块及其功能，扩展了你对 Android 应用程序的构造原理的了解。

### <a name="android-application-building-blocks"></a>Android 应用程序构建基块

Android 应用程序由特殊 Android 类的集合组成，这些类称为应用程序块  ，与任何数量的应用资源（图像、主题、帮助程序类等）捆绑在一起。&ndash; 这些类通过称为 Android 清单  的 XML 文件进行协调。

应用程序块组成 Android 应用程序的主干，因为它们使你可以执行使用常规类通常无法完成的操作。 两个最重要的块是 _活动_ 和 _服务_ ：

- **活动** &ndash; 活动与具有用户界面的屏幕对应，在概念上类似于 Web 应用程序中的网页。 例如，在新闻源应用程序中，登录屏幕会是第一个活动，新闻项的可滚动列表会是另一个活动，而每个项的详细信息页面会是第三个活动。 可以在[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)指南中了解有关活动的详细信息。

- **服务** &ndash; Android 服务通过接管长时间运行的任务并在后台运行它们来支持活动。 服务没有用户界面，用于处理未绑定到屏幕的任务 &ndash; 例如，在后台播放歌曲或将照片上传到服务器。 有关服务的详细信息，请参阅[创建服务](~/android/app-fundamentals/services/index.md)和 [Android 服务](~/android/app-fundamentals/services/index.md)指南。

Android 应用程序可能不会使用所有类型的块，通常具有一种类型的多个块。 例如，来自 [Hello，Android 快速入门](~/android/get-started/hello-android/hello-android-quickstart.md)的 Phoneword 应用程序只由一个活动（屏幕）和一些资源文件组成。 简单音乐播放器应用可能具有多个活动以及一个用于在应用处于后台时播放音乐的服务。

### <a name="intents"></a>意向

Android 应用程序中的另一个基本概念是意向  。
Android 围绕最小特权原则  进行设计 &ndash; 应用程序只能访问它们正常工作所需的块，它们对组成操作系统或其他应用程序的块具有有限的访问权限。 同样，块是松散耦合  的 &ndash; 它们设计为对其他块（甚至是属于同一应用程序的块）知之甚少且仅有有限的访问权限。

为了进行通信，应用程序块会来回发送异步消息（称为意向  ）。 意向包含有关接收块的信息，有时还包含一些数据。 从一个应用组件发送的意向会触发某个事件在其他应用组件中发生，从而将两个应用组件绑定在一起并允许它们进行通信。 通过来回发送意向，你可以使块来协调复杂操作（如启动相机应用以进行拍摄和保存、收集位置的信息或从一个屏幕导航到下一个屏幕）。

### <a name="androidmanifestxml"></a>AndroidManifest.XML

将一个块添加到应用程序时，它会向称为 **Android 清单** 的特殊 XML 文件进行注册。 清单会跟踪应用程序中的所有应用程序块，以及版本要求、权限和链接库 &ndash; 操作系统为使应用程序正常运行而需要了解的所有内容。 **Android 清单** 也可与活动和意向配合工作，以控制适合于给定活动的操作。 [使用 Android 清单](~/android/platform/android-manifest.md)指南中介绍了 Android 清单 的这些高级功能。

在单屏幕版本的 Phoneword 应用程序中，只使用了一个活动、一个意向和 `AndroidManifest.xml`，以及其他资源（如图标）。 在多屏幕版本的 Phoneword 中，添加了一个其他活动；它使用意向从第一个活动启动。 下一部分探讨意向如何帮助在 Android 应用程序中创建导航。

## <a name="android-navigation"></a>Android 导航

意向过去用于在屏幕之间进行导航。 现在可以深入了解此代码，以了解意向的工作原理并了解它们在 Android 导航窗格中的角色。

### <a name="launching-a-second-activity-with-an-intent"></a>使用意向启动第二个活动

在 Phoneword 应用程序中，意向用于启动第二个屏幕（活动）。 首先创建一个意向，传入当前上下文  （`this`，用于引用当前**上下文**）以及你所查找的应用程序块的类型 (`TranslationHistoryActivity`)：

```csharp
Intent intent = new Intent(this, typeof(TranslationHistoryActivity));
```

**上下文**是与有关应用程序环境的全局信息之间的接口 &ndash; 它使新创建的对象可以了解应用程序的进展情况。 如果将意向视为消息，则要提供消息收件人的名称 (`TranslationHistoryActivity`) 和接收方的地址 (`Context`)。

Android 提供一个选项，用于将简单数据附加到意向（复杂数据以不同方式进行处理）。 在 Phoneword 示例中，`PutStringArrayExtra` 用于将电话号码列表附加到意向，而 `StartActivity` 会对意向的收件人进行调用。 完整代码如下所示：

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", _phoneNumbers);
    StartActivity(intent);
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

Phoneword 应用程序引入了多个本指南中未提及的概念。 这些概念包括：

**字符串资源** &ndash; 在 Phoneword 应用程序中，`TranslationHistoryButton` 的文本设置为 `"@string/translationHistory"`。 `@string` 语法表示字符串的值存储在 _字符串资源文件_ (**Strings.xml**) 中。 `translationHistory` 字符串的以下值已添加到 **Strings.xml** 中：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Call History</string>
</resources>
```

有关字符串资源和其他 Android 资源的详细信息，请参阅 [Android 资源指南](~/android/app-fundamentals/resources-in-android/index.md)。

**ListView 和 ArrayAdapter** &ndash; _ListView_ 是一个 UI 组件，它提供了显示行的滚动列表的简单方法。 `ListView` 实例需要 _适配器_ ，以向它馈送行视图中包含的数据。 下面的代码行用于填充 `TranslationHistoryActivity` 的用户界面：

```csharp
this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
```

Listview 和适配器不在此文档的讨论范围内，但它们在非常全面的 [Listview 和适配器](~/android/user-interface/layouts/list-view/index.md) 指南中进行了介绍。
[使用数据填充 ListView](~/android/user-interface/layouts/list-view/populating.md) 专门处理使用内置 `ListActivity` 和 `ArrayAdapter` 类来创建并填充 `ListView`，而无需定义自定义布局（如 Phoneword 示例中所进行的那样）。

## <a name="summary"></a>总结

祝贺，你已完成第一个多屏 Android 应用程序！ 本指南介绍了 *Android 应用程序构建基块* 和 *意向*，并使用它们生成了一个多屏幕 Android 应用程序。 现在，你已具有坚实的基础，可开始开发自己的 Xamarin.Android 应用程序。

接下来，你会在[生成跨平台应用程序指南](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)中了解如何使用 Xamarin 生成跨平台应用程序。
