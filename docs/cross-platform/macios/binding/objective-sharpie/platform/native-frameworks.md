---
title: 绑定本机框架
description: 本文档介绍了如何使用客观 Sharpie 的框架选项创建到作为框架分发的库的绑定。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 0733e2b406f5032a2e2717df66c96dcb28301f09
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374126"
---
# <a name="binding-native-frameworks"></a>绑定本机框架

有时，本机库以[框架](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)的形式分发。 客观 Sharpie 提供一项便利功能，通过 `-framework` 选项绑定正确定义的框架。

例如，绑定适用于 iOS 的[Adobe 创造性 SDK 框架](https://creativesdk.adobe.com/downloads.html)非常简单：

```
$ sharpie bind \
    -framework ./AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

在某些情况下，框架将指定**info.plist** ，它指示框架应编译到的 SDK。 如果此信息存在并且未传递显式 `-sdk` 选项，则目标 Sharpie 将从框架的**info.plist** （`DTSDKName` 键或 `DTPlatformName` 和 `DTPlatformVersion` 键的组合）中推断出此信息。

`-framework` 选项不允许传递显式标头文件。 根据框架名称，按约定选择伞标头文件。 如果找不到伞标头，则目标 Sharpie 不会尝试绑定框架，并且必须通过提供要分析的正确伞头文件以及 clang 的任何框架自变量（如 `-F` framework 搜索路径选项），手动执行绑定。

在后台，指定 `-framework` 只是一个快捷方式。 以下绑定参数与上述 `-framework` 简写相同。
特别重要的是提供给 clang 的 `-F .` 框架搜索路径（请注意，这是命令的一部分所需的空间和句点）。

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    ./AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
