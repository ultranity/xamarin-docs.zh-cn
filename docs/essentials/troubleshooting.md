---
title: Xamarin.Essentials:疑难解答
description: 本文档介绍如何解决使用 Xamarin.Essentials 库进行开发时遇到的问题。
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2bd537a782b7090207b09ca02c5dfe5c4422a9ad
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545137"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials:疑难解答

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>错误：检测到 Xamarin.Android.Support.Compat 存在版本冲突

更新 NuGet 包（或添加新包）时，如果其中某 Xamarin.Forms 项目使用 Xamarin.Essentials，则可能出现以下错误：

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

问题在于两个 NuGet 的依赖项不匹配。 通过手动添加同时支持两者的特定版本的依赖项（在本例中为 Xamarin.Android.Support.Compat）可解决此问题  。

为此，请手动添加冲突源 NuGet，并使用“版本”列表选择特定版本  。 目前，使用版本为 28.0.0.3 的 Xamarin.Android.Support.Compat 和 Xamarin.Android.Support.Core.Util NuGet 可修复此错误。

有关详细信息，请参阅[此博客文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)以及有关如何解决此问题的视频。

如果遇到任何问题或发现 bug，请在 [Xamarin.Essentials GitHub 存储库](https://github.com/xamarin/Essentials)上报告。
