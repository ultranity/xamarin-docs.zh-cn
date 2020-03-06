---
title: Xamarin Profiler
description: 本指南将探讨 Xamarin Profiler 的主要功能。 它查看探查器、分析和使用的时间，以及用于分析 Xamarin 应用程序的标准工作流。
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: davidortinau
ms.author: daortin
ms.date: 06/03/2018
ms.openlocfilehash: cb109055b476fee6d34041940c682187074f4e7d
ms.sourcegitcommit: 099b06e311a40c00eeea85465ff9b97867a5c5de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295438"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_本指南将探讨 Xamarin Profiler 的主要功能。它查看探查器、分析和使用的时间，以及用于分析 Xamarin 应用程序的标准工作流。_

应用程序的成功与否取决于最终用户体验。 作为开发人员，你可能已在应用程序中实现了一些极为出色的功能，但如果应用程序的崩溃或完全崩溃，用户可能会被删除。

从历史上看，Mono 提供了强大的命令行探查器，用于收集在 Mono 运行时中运行的程序的相关信息，称为[mono 日志探查器](https://www.mono-project.com/docs/debug+profile/profile/profiler/)。 Xamarin Profiler 单色日志探查器的图形界面，并支持在 Mac 上分析 Android、iOS、tvOS 和 Mac 应用程序，以及 Windows 上的 Android、iOS 和 tvOS 应用程序。

Xamarin Profiler 提供了许多可用于分析的乐器，即分配、周期和时间探查器。 本指南探讨了这些乐器度量值以及它们如何分析应用程序，并说明了每个屏幕上显示的数据的含义。

本指南介绍常见分析方案，并将探查器引入为帮助分析和优化 iOS 和 Android 应用程序的工具。

## <a name="download-and-install"></a>下载并安装

> [!NOTE]
> 你需要成为[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/)订阅者，才能在 Windows 上的 Visual Studio Enterprise 或 Mac 上的 Visual Studio for Mac 解锁此功能。

Xamarin Profiler 是独立的应用程序，并且与 Visual Studio for Mac 和 Visual Studio 集成，可从 IDE 内部进行分析。

下载适用于你的平台的安装包：

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac-1.6.13-11.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/XamarinProfiler.Windows.Installer.1.6.10-15.msi)

下载后，启动安装程序以将 Xamarin Profiler 添加到系统中。

## <a name="profilers-and-profiling"></a>探查器和分析

在应用程序开发中，事件探查是一个重要且经常被忽略的步骤。 分析是**动态程序分析**的一种形式，它在程序运行和使用时对其进行分析。 探查器是一种数据挖掘工具，用于收集有关时间复杂性、特定方法使用情况和所分配内存的信息。 探查器使您可以深入了解和分析这些指标，以便在代码中查明问题的区域。

设计和开发应用程序时，不要提前优化，这一点很重要;这就是在很少被访问的领域中花费时间来开发您的代码。 这就是分析的强大之处。 探查器可让你深入了解你的基本代码的最常用部分，并帮助找出你应花时间进行改进的领域。 开发人员应小心了解应用程序中大部分时间的使用情况，以及应用程序如何使用内存。

分析在所有类型的开发中都很有用，但在移动开发中尤其重要。 与台式计算机相比，未优化的代码在移动平台上更为明显，应用的成功与否取决于有效运行的精美且优化的代码。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler 为开发人员提供了一种方法，用于分析 Visual Studio for Mac 或 Visual Studio 内部的应用程序。 探查器收集并显示有关应用程序的信息，开发人员可以使用该信息分析应用程序的行为。 可以通过多种不同的方法使用 Xamarin Profiler 分析应用程序，即内存分析和统计采样。 它们分别通过分配和时间探查器检测。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

目前，Xamarin Profiler 可用于测试 Mac 上的 Xamarin、Xamarin 和 Xamarin 应用程序（通过 Visual Studio for Mac）。 探查器是一个独立于 IDE 的进程，因此，除了从 Visual Studio for Mac 启动之外，它还可用作独立的应用程序来检查从[mono 日志探查器](https://www.mono-project.com/docs/debug+profile/profile/profiler/)生成的 .exe 和 `.mlpd` 文件。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

目前，Xamarin Profiler 可用于在 Windows 上测试 Xamarin Android 应用（通过 Visual Studio 和 Visual Studio for Mac）。 探查器是一个独立于 IDE 的进程，因此，除了从 Visual Studio 启动以外，它还可用作独立的应用程序，用于检查从[mono 日志探查器](https://www.mono-project.com/docs/debug+profile/profile/profiler/)生成的 .exe 和 `.mlpd` 文件。

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>探查器支持

以下平台提供对 Xamarin Profiler 的支持：

- Visual Studio for Mac （macOS，具有 Enterprise License）
  - Android
    - 设备和模拟器
  - iOS
    - 设备和模拟器
  - tvOS （不支持时间检测）
    - 设备和模拟器
  - Mac

- Visual Studio （仅适用于**企业**版）
  - Android
    - 设备和模拟器
  - iOS [实验]
    - 设备和模拟器
  - tvOS
    - 设备和模拟器

请注意，**只能**对**调试**配置进行分析。

## <a name="profiler-basics"></a>探查器基础知识

本部分介绍 Xamarin Profiler 的各个部分并演示其功能。

### <a name="allow-profiling-in-your-app"></a>允许在应用程序中进行分析

你需要允许在应用的项目选项中进行分析，然后才能成功分析你的应用。

- iOS：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

  **生成 > iOS 调试 > 启用分析**

  ![Visual Studio for Mac 中的 "iOS 选项" 对话框](images/ios-options-mac.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  **> IOS Build > 启用分析的属性**

  ![Visual Studio 中的 "iOS 选项" 对话框](images/ios-project-options-vs.png)

-----

- Android：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

  **生成 > Android 调试 > 启用开发人员检测**

  ![Visual Studio for Mac 中的 "Android 选项" 对话框](images/android-project-options.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  **生成 > Android 调试 > 启用开发人员检测**

  ![Visual Studio for Mac 中的 "Android 选项" 对话框](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>启动探查器

在分析 iOS 或 Android 应用程序时，或作为独立的应用程序，可以从 IDE 中启动 Xamarin Profiler。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>从 Visual Studio for Mac 启动

1. 首先，请确保已在 Visual Studio for Mac 中加载应用程序，并选择 "（默认）" "调试" 配置。
2. 浏览以**运行 >** 在 Visual Studio for Mac 中开始分析，或在 Visual Studio 中**分析 > Xamarin Profiler**以打开探查器，如下图所示：

  ![从 Visual Studio for Mac 启动探查器](images/start-profiling-xs.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>从 Visual Studio 启动

1. 首先，请确保已在 Visual Studio 中加载应用程序，并选择 "（默认）" 调试配置，如上所述。
2. 浏览以分析 Visual Studio 中的 **> Xamarin Profiler** ，以打开探查器，如下图所示：

![从 Visual Studio 启动探查器](images/start-profiling-vs.png)

-----

如果菜单项未出现，请参阅[故障排除指南](~/tools/profiler/troubleshooting.md)。

这会启动探查器，并自动开始分析应用程序。

探查器可用于测量内存和性能。 它通过分配和时间探查器检测来实现此目标，我们将在下一节将对此进行详细探讨。

#### <a name="saving-and-loading-profiler-sessions"></a>保存和加载探查器会话

若要随时保存分析会话，请从探查器菜单栏中选择 " **> 文件" "另存为 ...** "。 这会将文件保存为 _.mlpd_格式，这是一种特殊的高度压缩格式，用于分析数据。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

安装后，可以在应用程序文件夹中找到 Xamarin Profiler，如以下屏幕截图所示：

![从 Mac 打开独立探查器](images/applications.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

安装后，可以在应用程序目录中找到 Xamarin Profiler 应用程序：

![从 Windows 打开独立探查器](images/applications-vs.png)

-----

可以通过打开独立的应用程序，选择 "**选择目标**" 并加载该文件，将 *.mlpd*文件加载到探查器中。

有关详细信息，请参阅[生成 .Mlpd 文件](~/tools/profiler/troubleshooting.md#gen_mlpd)。

## <a name="profiler-features"></a>探查器功能

Xamarin Profiler 由五个部分组成，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[Visual Studio for Mac 中的 ![探查器部分](images/profiler-mac-sml.png)](images/profiler-mac.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[Visual Studio 中的 ![探查器部分](images/profiler-vs.png)](images/profiler-vs.png#lightbox)

-----

- **工具栏**-位于探查器顶部，这提供了启动/停止分析、选择目标进程、查看应用程序运行时间以及组成探查器应用程序的拆分视图的选项。
- **检测列表**–此列表列出为分析会话加载的所有仪器。
- **绘图图**–这些图表水平与检测列表中的相关乐器相关。 可以使用滑块（在时间探查器下显示）来更改刻度。
- **检测详细信息区域**-包含当前乐器的选定视图显示的数据。 在下面的部分中，我们将更详细地介绍这些视图。
- **检查器视图**–其中包含可由分段控件选择的部分。 这些部分依赖于所选检测，其中包括：配置设置、统计信息、堆栈跟踪信息和根的路径。

### <a name="allocations"></a>分摊

当创建和垃圾回收应用程序时，分配检测会提供有关该应用程序中的对象的详细信息。

探查器的顶部是 "分配" 图表，其中显示在分析期间按固定时间间隔分配的内存量。 当前，分配关系图是在该时间点的分配总数，而不是堆的大小。 在某种意义上，它永远不会关闭，只会增加。 这包括在堆栈上分配的对象。 根据所使用的运行时版本，图表可能会有所不同，甚至对于同一应用也是如此。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![分配检测](images/allocations1.png)](images/allocations1.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![分配检测](images/allocations1-vs.png)](images/allocations1-vs.png#lightbox)

-----

分配检测中存在不同的数据视图，它们允许开发人员分析其应用程序使用和释放内存的方式。 下面介绍了这些视图：

- **分配**–显示所有分配的列表，并按类名称对其进行分组。 这为使用的类和方法提供了很好的概览、使用的频率以及使用的类的集合大小。 双击类将显示分配的内存： 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

  [![分配 "选项卡](images/allocations3.png)](images/allocations3.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  [![分配 "选项卡](images/allocations2-vs.png)](images/allocations2-vs.png#lightbox)

-----

分配的检查器视图提供了用于筛选和分组对象的选项，提供了有关分配的内存和最大分配的统计信息，以及用于堆栈跟踪的视图和指向根路径的路径。

- "**调用树**" –这会显示应用程序中所有线程的整个调用树，并包括有关在每个节点上分配的内存的信息。 如果在列表中选择了某个元素，则所有同级节点都将显示为灰色。 您可以展开树，或双击元素向下钻取。显示此数据视图时，可以使用 "显示设置检查器" 视图更改其显示方式。 目前有两个选项：
    1. **反转调用树**–这会将堆栈跟踪从上到下。 这是一个方便的视图选项，因为它表示 CPU 花费了时间的最深层方法。
    2. **按线程分隔**–此选项按线程组织调用树。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

  [![调用关系树选项卡](images/allocations2.png)](images/allocations2.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  [![调用关系树选项卡](images/allocations3-vs.png)](images/allocations3-vs.png#lightbox)

-----

- **快照**–此窗格显示有关内存快照的信息。 若要在分析实时应用程序时生成这些内容，请在工具栏上单击要查看保留和释放内存的每个点的_相机_按钮。 然后，你可以单击每个快照以了解在后台发生的情况。 请注意，仅当实时分析应用程序时，才可以执行快照。 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

  [![快照 "选项卡](images/allocations4.png)](images/allocations4.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  [![快照 "选项卡](images/allocations4-vs.png)](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>时间探查器

时间探查器检测在应用程序的每个方法中所花费的时间。 应用程序将定期暂停，并在每个活动线程上运行堆栈跟踪。 "检测详细信息" 区域中的每一行都显示已遵循的执行路径。

如以下屏幕截图中所示，绘图图显示应用程序在运行时收到的样本数：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![时间探查器检测](images/time1.png)](images/time1.png#lightbox) 

[![时间探查器仪器–示例列表](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![时间探查器检测](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![时间探查器仪器–示例列表](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- "**调用树**"-显示每个方法所用的时间量：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

  [![时间探查器仪器–调用树](images/time2.png)](images/time2.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  [![时间探查器仪器–调用树](images/time2-vs.png)](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Cycles

通过使用C#和F#托管代码，它可能很常见，并且很容易创建对对象的引用，这将永远不会被释放。 此检测允许你查找这些对象，并显示应用程序中引用的循环。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![循环检测](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![循环检测](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>分析应用程序

目前只能分析默认的调试配置。

如果使用任何其他配置来分析应用，则会显示以下消息对话框：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![分析错误对话框](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![分析错误对话框](images/image1vs.png)](images/image1vs.png#lightbox) 

-----

选择 "**更新**" 以继续。

### <a name="sgen-garbage-collector-and-profiling"></a>SGen 垃圾回收器和分析

[SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)垃圾回收器用于所有 Xamarin 平台。

SGen 是一个代垃圾回收器，它将应用程序的对象分配给三个堆：小堆、主堆和大型对象空间。 这允许 speedier 执行垃圾回收。 SGen 当前是 Xamarin 和 Xamarin 统一应用程序的默认 GC。

使用 Classic API 的 Xamarin iOS 应用程序使用 Boehm GC –保守的非代垃圾回收器。 由于保守，因此释放可用内存的可能性不大，在使用探查器时可能导致不准确的结果。 出于此原因，分配检测不能用于 Boehm 垃圾回收器。

当你的应用程序使用 Boehm GC 时，如果你的应用程序使用了 GC，Xamarin 不建议将使用 Boehm 的现有 iOS 应用程序切换到 SGen，而无需仔细研究和全面测试。 Xamarin 还不建议切换到 SGen 进行分析，然后再切换回来，因为这些结果不提供内存使用的准确基准。

有关内存管理的详细信息，请参阅[内存和性能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南。

## <a name="summary"></a>总结

在本指南中，我们介绍了哪些分析是什么，以及如何对开发人员有利。 接下来，我们介绍了 Xamarin Profiler，并向其工作方式提供一些历史记录和信息。 最后，我们一起了 Xamarin Profiler 的功能，并探讨了分配和时间探查器仪器。

## <a name="related-links"></a>相关链接

- [内存和性能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [发行说明](/xamarin/tools/profiler/release-notes/)
