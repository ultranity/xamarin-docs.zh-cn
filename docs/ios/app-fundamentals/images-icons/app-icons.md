---
title: Xamarin 中的应用程序图标
description: 本文档介绍如何在 Xamarin 中使用各种应用程序图标：应用程序图标本身、聚焦图标、设置图标和 iTunes 作品。
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2017
ms.openlocfilehash: 37695ef93a1005febf12369e7d1defccf6130832
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916549"
---
# <a name="application-icons-in-xamarinios"></a>Xamarin 中的应用程序图标

将详细介绍以下主题：

- "[应用程序"、"聚光灯" 和 "设置" 图标](#icon-types)-IOS 应用程序所需的不同类型的图标。
- 使用资产[目录管理图标](#managing)-使用资产目录管理应用程序图标。
- [ITunes 图稿](#itunes)-为提供应用程序的即席方法提供所需的 iTunes 作品。

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>"应用程序"、"聚光灯" 和 "设置" 图标

与 Xamarin iOS 应用程序可以将图像资产用于 UI 控件和文档图标一样，可以使用图像资产来提供应用程序图标。 IPad 中的以下屏幕截图说明了 iOS 中图标的三个用途：

- **应用程序图标**-每个 iOS 应用都必须定义一个应用程序图标。 这是用户将在 iOS 主屏幕上点击以启动应用程序的图标。 此外，Game Center 使用此图标（如果适用）。 示例： 

    [![](app-icons-images/000.png "Application Icon")](app-icons-images/000-full.png#lightbox)
- **聚光灯图标**-当用户在聚焦搜索中输入应用名称时，将显示此图标。 示例： 

    [![](app-icons-images/000a.png "Spotlight Icon")](app-icons-images/000a-full.png#lightbox)
- "**设置" 图标**-如果用户在其 iOS 设备上输入 "**设置**" 应用，则此图标将显示在应用的 "**设置**" 列表的末尾。 示例： 

    [![](app-icons-images/000b.png "Settings Icon")](app-icons-images/000b-full.png#lightbox)

需要以下映像资产大小和分辨率，以支持适用于 iOS 应用的、面向 iOS 5 到 iOS 9 （或更高版本）的 Xamarin iOS 应用所需的所有图标类型：

### <a name="iphone-icon-sizes"></a>iPhone 图标大小

- **iPhone： iOS 9 & 10 （iPhone 6 & 7 Plus）**

    ||3倍|
    |---|---|
    |应用程序图标|180x180|
    |聚焦|120x120|
    |设置|87x87|

- **iPhone： iOS 7 & 8**

    ||1 倍|2x|
    |---|---|---|
    |应用程序图标|对<sup>1</sup>|120x120|
    |聚焦|40x40<sup>2</sup>|80x80|
    |设置|-|-|

- **iPhone： iOS 5 & 6**

    ||1 倍|2x|
    |---|---|---|
    |应用程序图标|57x57|114x114|
    |聚焦|29x29|58x58|
    |设置|29x29<sup>3、4</sup>|58x58<sup>3、4</sup>|

### <a name="ipad-icon-sizes"></a>iPad 图标大小

- **iPad： iOS 9 & 10**

    ||2x （iPad Pro）|
    |---|---|
    |应用程序图标|167x167<sup>6</sup>|
    |聚焦|120x120<sup>6</sup>|
    |设置|58x58<sup>5</sup>|

- **iPad： iOS 7 & 8**

    ||1 倍|2x|
    |---|---|---|
    |应用程序图标|76x76|152x152|
    |聚焦|40x40|80x80|
    |设置|-|-|

- **iPad： iOS 5 & 6**

    ||1 倍|2x|
    |---|---|---|
    |应用程序图标|72x72|144x144|
    |聚焦|50x50|100x100|
    |设置|29x29<sup>3、5</sup>|58x58<sup>3、5</sup>|

 1. Visual Studio for Mac 和 Xcode 不再支持为 iOS 7 设置1x 映像。
 2. 使用资产目录时不支持为 iOS 7 设置1x 映像。
 3. iOS 7 & 8 使用与 iOS 5 & 6 相同的映像大小。
 4. 与聚光灯图标使用相同的图像和大小。
 5. 使用与 iPhone 相同的大小图标。
 6. 仅支持资产目录映像集。

 有关图标的详细信息，请参阅 Apple 的[图标和图像大小](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1)文档。

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>管理具有资产目录的图标

对于图标，可以将特殊 `AppIcon` 图像集添加到应用项目中的 `Assets.xcassets` 文件。 支持所有解决方案所需的所有映像版本都包含在_xcasset_中，并组合在一起。 Visual Studio for Mac 中的一个特殊编辑器允许开发人员以图形方式包含和设置这些图像。

若要使用资产目录，请执行以下步骤：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 双击**解决方案资源管理器**中的 `Info.plist` 文件，将其打开进行编辑。
2. 向下滚动到 " **IPhone 图标**" 部分。
3. 单击 "**迁移到资产目录**" 按钮：

    ![](app-icons-images/migrate01.png "Ensure AppIcon is selected")

4. 在**解决方案资源管理器**中，双击 `Assets.xcassets` 文件以将其打开进行编辑： 

    ![](app-icons-images/asset01.png "The Assets.xcassets file in the Solution Explorer")

5. 从资产列表中选择 `AppIcon`，以显示 `Icon Editor`：

    ![](app-icons-images/asset02.png "The AppIcon editor")

6. 单击给定的图标类型，并为所需的类型/大小选择图像文件，或者从文件夹中拖放到所需大小。
7. 单击 "**打开**" 按钮，将图像包含在项目中，并在 xcasset 中对其进行设置。
8. 对所需的所有映像重复此操作。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 双击 "信息"。  \* ***解决方案资源管理器**中的文件：

    ![](app-icons-images/icon01w.png "Select Info.plist")

2. 单击 "**视觉资产**" 选项卡，然后在 "**应用程序图标**" 下单击 "**使用资产目录**" 按钮： 

    ![](app-icons-images/icon02w.png "Select the Visual Assets tab")

    如果没有按钮，而是下拉列表，则资产目录已添加到此项目中。

3. 在**解决方案资源管理器**中，展开 "**资产目录**" 文件夹： 

    ![](app-icons-images/image009.png "Expand the Asset Catalog folder")

4. 双击**媒体**文件以在编辑器中将其打开： 

    ![](app-icons-images/image010.png "Open the Media file in the editor")

5. 在**属性资源管理器**中，开发人员可以选择所需的图标的不同类型和大小。
6. 单击给定的图标类型，然后选择所需类型/大小的图像文件。
7. 单击 "**打开**" 按钮，将图像包含在项目中，并在 xcasset 中对其进行设置。
8. 对所需的所有映像重复此操作。

-----

这是包括和管理将用于为应用程序提供应用程序、聚焦和设置图标的图像资产的首选方法。

<a name="itunes" />

## <a name="itunes-artwork"></a>iTunes 图稿

如果使用提供应用的即席方法（适用于企业用户或在真实设备上进行 beta 测试），开发人员还需要包括一个512x512 和一个1024x1024 映像，该映像将用于表示 iTunes 中的应用。

若要指定 iTunes 图稿，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 双击**解决方案资源管理器**中的 `Info.plist` 文件，将其打开进行编辑。
2. 滚动到编辑器的 " **ITunes 图稿**" 部分： 

    ![](app-icons-images/itunes01.png "Scroll to the iTunes Artwork section of the editor")
3. 对于任何缺少的图像，请单击编辑器中的缩略图，从 "打开文件" 对话框中选择所需 iTunes 图稿的图像文件，然后单击 **"确定"** 按钮。
4. 重复此步骤，直到已为该应用指定所有所需的映像。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 双击**解决方案资源管理器**中的 `Info.plist` 文件，将其打开进行编辑。

2. 单击 "**视觉资产**" 选项卡，然后展开**iTunes 图稿**： 

    ![](app-icons-images/itunes01w.png "Editing iTunes Artwork in Visual Studio")
3. 对于任何缺少的图像，请单击编辑器中的缩略图，从 "打开文件" 对话框中选择所需 iTunes 图稿的图像文件，并单击 "**打开**" 按钮。
4. 重复此步骤，直到已为该应用指定所有所需的映像。

-----

## <a name="related-links"></a>相关链接

- [使用图像（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和图像创建准则](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)）
