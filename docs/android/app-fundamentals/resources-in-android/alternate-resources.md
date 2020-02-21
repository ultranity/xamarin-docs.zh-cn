---
title: 备用资源
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 644262310614874794810fd083ba1823abfd0da2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025419"
---
# <a name="alternate-resources"></a>备用资源

备用资源是面向特定设备或运行时配置（如当前语言、特定屏幕大小或像素密度）的资源。 如果 Android 可以匹配比默认资源更具体的特定设备或配置的资源，则将改为使用该资源。 如果找不到与当前配置匹配的替代资源，将加载默认资源。 下面的 "资源位置" 部分中详细介绍了 Android 如何确定应用程序将使用的资源

根据资源类型，备用资源在 Resources 文件夹内作为子目录进行组织，就像默认资源。 备用资源子目录的名称格式为： _ResourceType_-_限定符_

*限定符*是用于标识特定设备配置的名称。
名称中可能有多个限定符，其中每个限定符都用破折号分隔。 例如，下面的屏幕截图显示了一个简单的项目，其中包含不同配置的备用资源，如区域设置、屏幕密度、屏幕大小和方向：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![备用资源](alternate-resources-images/alternate-resources-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![备用资源](alternate-resources-images/alternate-resources-xs.png)

-----

将限定符添加到资源类型时，下列规则适用：

1. 可能有多个限定符，每个限定符都用破折号分隔。

2. 限定符仅指定一次。

3. 限定符必须按下表中显示的顺序排列。

下面列出了可能的限定符供参考：

- **MCC 和 MNC** &ndash;[移动国家/地区代码](https://en.wikipedia.org/wiki/List_of_mobile_country_codes)（MCC）以及（可选）[移动网络代码](https://en.wikipedia.org/wiki/Mobile_Network_Code)（MNC）。 SIM 卡将提供 MCC，而设备连接到的网络将提供 MNC。 尽管可以使用移动国家/地区代码定位区域设置，但建议使用下面指定的语言限定符。 例如，若要将资源设定为德国，将 `mcc262`限定符。 若要在美国的 T-Mobile 目标资源，请 `mcc310-mnc026`限定符。
  有关移动国家/地区代码和移动网络代码的完整列表，请参阅 <http://mcc-mnc.com/>。

- **Language** &ndash; 两字母[iso 639-1 语言代码](https://en.wikipedia.org/wiki/ISO_639-1)，并可选择后跟两个字母的[iso-3166-2 个区域代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)。 
  如果同时提供了这两个限定符，则使用 `-r`分隔它们。 例如，若要以法语区域设置为目标，则使用 `fr` 的限定符。 若要以法语加拿大区域设置为目标，将使用 `fr-rCA`。 有关语言代码和区域代码的完整列表，请参阅语言名称和[国家/地区名称和代码元素](https://www.iso.org/iso-3166-country-codes.html)[名称表示形式的代码](https://www.loc.gov/standards/iso639-2/php/English_list.php)。

- **最小宽度**&ndash; 指定应用程序要在其上执行的最小屏幕宽度。 更详细地介绍[了如何为不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别13（Android 3.2）及更高版本中可用。 例如，限定符 `sw320dp` 用于将高度和宽度至少为320dp 的设备设定为目标。

- "**可用宽度**" &ndash; 屏幕的最小宽度，格式为 w*N*dp，其中*N*是以密度无关的像素为单位的宽度。
  此值可能会在用户旋转设备时更改。 更详细地介绍[了如何为不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别13（Android 3.2）及更高版本中可用。 示例：限定符 w720dp 用于针对宽度至少为720dp 的设备。

- **可用高度**&ndash; 采用 h*N*dp 格式的屏幕的最小高度，其中*N*是 dp 中的高度。 此值可能会在用户旋转设备时更改。 更详细地介绍[了如何为不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别13（Android 3.2）及更高版本中可用。 例如，限定符 h720dp 用于针对高度至少为720dp 的设备

- **屏幕大小**&ndash; 此限定符是这些资源的屏幕大小的泛化。 更详细地介绍了如何[为不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  可能值为 `small`、`normal`、`large` 和 `xlarge`。 在 API 级别9中添加（Android 2.3/Android 2.3.1/Android 2.3.2）

- **屏幕方位**&ndash; 这基于纵横比，而不是屏幕方向。 长屏幕较宽。 在 API 级别4（Android 1.6）中添加。 可能的值为 long 和 notlong。

- **屏幕方向**&ndash; "纵向" 或 "横向" 屏幕方向。 这可能会在应用程序的生存期内发生更改。
  可能的值为 `port` 和 `land`。

- 用于汽车 dock 或书桌 dock 中设备的**停靠模式**&ndash;。 在 API 级别8（Android 2.2. x）中添加。 可能的值为 `car` 和 `desk`。

- **夜间模式**&ndash; 应用程序是否在晚上或当天运行。 这可能会在应用程序的生存期内发生更改，并旨在使开发人员能够在晚间使用较暗版本的接口。 在 API 级别8（Android 2.2. x）中添加。 可能的值为 `night` 和 `notnight`。

- **屏幕像素密度（dpi）** &ndash; 物理屏幕上指定区域中的像素数。 通常表示为每英寸点数（dpi）。 可能的值有：

  - &ndash; 低密度屏幕 `ldpi`。

  - `mdpi` &ndash; 中等密度屏幕

  - `hdpi` &ndash; 高密度屏幕

  - &ndash; 额外的高密度屏幕 `xhdpi`

  - `nodpi` 不会缩放 &ndash; 资源

  - 在包括和 hdpi 之间的屏幕的 API 级别13（Android 3.2）中引入 `tvdpi` &ndash;。

- **触摸屏类型**&ndash; 指定设备可能具有的触摸屏的类型。 可能的值为 `notouch` （无触摸屏）、`stylus` （适合触笔的电阻式触摸屏）和 `finger` （触摸屏）。

- **键盘可用性**&ndash; 指定哪种类型的键盘可用。 这可能会在应用程序的生存期内改变 &ndash; 例如，当用户打开硬件键盘时。 可能的值有：

  - `keysexposed` &ndash; 设备具有可用的键盘。 如果未启用软件键盘，则仅在打开硬件键盘时使用。

  - `keyshidden` &ndash; 设备具有硬件键盘，但它处于隐藏状态且未启用软件键盘。

  - `keyssoft` &ndash; 设备启用了软件键盘。

- **主文本输入法**&ndash; 用于指定可用于输入的硬件密钥类型。 可能的值有：

  - `nokeys` &ndash; 没有用于输入的硬件密钥。

  - `qwerty` &ndash; 有一个可用的标准键盘。

  - `12key` &ndash; 有一个12键的硬件键盘

- **导航键可用性**&ndash; 适用于5向或3-d （定向）导航的时间。 这可能会在应用程序的生存期内发生更改。 可能的值有：

  - `navexposed` &ndash; 可供用户使用的导航键

  - 导航键 &ndash; `navhidden` 不可用。

- **主非触摸式导航方法**&ndash; 设备上可用的导航类型。 可能的值有：

  - `nonav` &ndash; 仅可用的导航工具是触摸屏

  - `dpad` &ndash; 的面板（方向面板）可用于导航

  - `trackball` &ndash; 设备具有导航轨迹球

  - `wheel` &ndash; 有一个或多个定向轮可用的不常见方案

- **平台版本（API 级别）** &ndash; 设备支持的 api 级别，格式为 v*n*，其中*N*是目标为的 api 级别。 例如，v11 将以 API 级别11（Android 3.0）设备为目标。

有关资源限定符的更完整信息，请参阅在 Android 开发人员网站上[提供资源](https://developer.android.com/guide/topics/resources/providing-resources.html)。

## <a name="how-android-determines-what-resources-to-use"></a>Android 如何确定要使用的资源

很可能，Android 应用程序将包含很多资源。 了解 Android 如何选择应用程序在设备上运行时的资源，这一点很重要。

Android 通过循环访问以下规则测试来确定资源基准：

- **消除矛盾限定符**&ndash; 例如，如果设备方向为纵向，则会拒绝所有横向资源目录。

- **不支持忽略限定符**&ndash; 并非所有限定符都适用于所有 API 级别。 如果资源目录包含设备不支持的限定符，则将忽略该资源目录。

- **确定下一个优先级最高的限定符**&ndash; 引用上表选择下一个优先级最高的限定符（从上到下）。

- 如果有任何资源目录与上表的限定符匹配，请**保留任何资源目录 &ndash; 限定符**，请选择下一个优先级最高的限定符（从上到下）。

以下流程图还演示了这些规则：

[![资源流程图](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

当系统正在查找特定于密度的资源，但找不到这些资源时，它将尝试查找其他密度特定的资源并对其进行缩放。 Android 不一定会使用默认资源。
例如，在查找低密度资源且不可用时，Android 可能会选择高密度版本的资源，而不是使用默认资源或中等密度资源。 这样做的原因是：高密度资源可按0.5 的比例缩小，这将导致更少的可见性问题，而不是缩小中等密度的资源，这将需要0.75。

例如，假设有以下可绘制资源目录的应用程序：

```
drawable
drawable-en
drawable-fr-rCA
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

现在，应用程序将在具有以下配置的设备上运行：

- **区域设置**&ndash; EN-GB
- **方向**&ndash; 端口
- **屏幕密度**&ndash; hdpi
- **触摸屏类型**&ndash; notouch
- **主输入法**&ndash; 12key

首先，消除了法语资源，因为它们与 `en-GB`的区域设置发生冲突，因此，我们将：

```
drawable
drawable-en
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

接下来，从上述限定符表中选择第一个限定符： MCC 和 MNC。 没有资源目录包含此限定符，因此将忽略 MCC/MNC 代码。

选择下一个限定符，即语言。 存在与语言代码匹配的资源。 与 `en` 语言代码不匹配的所有资源目录都将被拒绝，因此资源的列表现在是：

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

存在的下一个限定符用于屏幕方向，因此消除了与 `port` 屏幕方向不匹配的所有资源目录：

```
drawable-en-port
drawable-en-port-ldpi
```

接下来是屏幕密度的限定符，`ldpi`，这将导致排除一个以上的资源目录：

```
drawable-en-port-ldpi
```

作为此过程的结果，Android 将使用资源目录中的可绘制资源 `drawable-en-port-ldpi` 设备。

> [!NOTE]
> 屏幕大小限定符为此选择过程提供了一个例外。 Android 可以选择为小于当前设备所提供的屏幕而设计的资源。 例如，大屏幕设备可以使用资源提供的标准大小的屏幕。 不过，这种情况并不是真正的：同一大屏幕设备不会使用为加大屏幕提供的资源。 如果 Android 找不到与给定屏幕大小匹配的资源集，应用程序会崩溃。
