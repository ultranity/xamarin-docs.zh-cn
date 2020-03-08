---
title: 在 Xamarin 中使用 tvOS 图标和图像
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中使用图标和图像。 它讨论了如何启动图像、分层图像、应用图标等。
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b1b6d07b221f702b54833bd87161d6abbadbd4e8
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915799"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>在 Xamarin 中使用 tvOS 图标和图像

创建 captivating 图标和图像是为你的 Apple TV 应用开发沉浸式用户体验的关键部分。 本指南将介绍为 Xamarin. tvOS apps 创建和包括必要的图形资产所需的步骤：

- [启动映像](#Launch-Image)-首次启动应用时，会显示一个启动图像，启动完成后，将由应用的第一个屏幕替换。
- 适用于 Apple TV、Apple 新分层图像的[分层图像](#Layered-Images)使用视差效果，为所选项目创建三维效果。 有多种方法可以[创建分层映像](#Creating-Layered-Images)。
- [应用图标](#App-Icons)-除了 Apple TV 主屏幕外，对于应用商店，还需要图标。 它们必须作为分层映像提供。
- [上架映像](#Top-Shelf-Image)-如果你的应用位于主屏幕的顶部，则需要使用一个顶部的图像来突出显示应用功能。 （可选）可以提供[动态上架内容](#Dynamic-Top-Shelf-Content)，以在应用中突出显示内容。
- [Game Center 图像](#Game-Center-Images)-如果你的应用程序是游戏并使用 Game Center，则需要几个附加映像。
- [设置 TvOS 项目映像](#Setting-Xamarin.tvOS-Project-Images)-介绍设置 tvOS 应用的启动映像和应用图标所需的步骤。

> [!IMPORTANT]
> Apple 电视上的所有映像都处于1x 分辨率（`@1x`），_只_应使用此大小的图像。 包括更大、更高分辨率的图形不仅需要下载和使用更多内存和存储，而且必须在运行时动态重新缩放，并对绘图性能产生负面影响。

<a name="Launch-Image" />

## <a name="launch-image"></a>启动映像

启动映像是首次在 Apple 电视上开始使用 tvOS 应用时显示的内容，因此，每个 tvOS 应用都必须提供启动映像。 

启动图像会快速显示，并为您的应用程序提供快速且响应迅速。 Apple TV 会将启动图像替换为应用程序的第一个屏幕不久之后。

启动图像不是广告或艺术表达式的一种机会，它们只是为了给您的应用程序快速启动并准备好使用的印象。

|启动映像大小|说明|
|---|---|
|1920x1080px|仅限非分层 .png 文件|

Apple 提供以下建议，用于设计应用程序的启动映像：

- 与**第一屏几乎相同**-启动屏幕应尽可能接近应用程序的第一个屏幕。 在第一个屏幕出现时，包括不同的图形或元素可能会导致 "闪光" 令人讨厌。
- **避免使用文本**启动图像是静态的，因此，在显示前将不会对其进行本地化。
- **Downplay 发布**-由于 Apple TV 用户经常切换应用，因此不应注意应用启动过程。
- **无广告或品牌**-您的启动映像不应用作 "关于" 屏幕或包含任何品牌，除非它是应用程序第一个屏幕的静态部分。 严格禁止广告。

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>设置启动图像

若要设置 tvOS 项目的启动映像，请执行以下操作：

1. 在**解决方案资源管理器**中，双击 "`Assets.xcassets`" 将其打开进行编辑： 

    [![](icons-images-images/asset01.png "The Assets.xcassets file")](icons-images-images/asset01.png#lightbox)
2. 在**资产编辑器**中，单击 "`LaunchImages` 资产： 

    [![](icons-images-images/asset02.png "The LaunchImages asset")](icons-images-images/asset02.png#lightbox)
3. 单击 " **1X APPLE TV** " 条目，并选择 "启动" 映像，或者在中从文件系统中选择一个新图像（可选）： 

    [![](icons-images-images/asset03.png "Select a Launch Image")](icons-images-images/asset03.png#lightbox)
4. 保存所做更改。

<a name="Layered-Images" />

## <a name="layered-images"></a>分层映像

对于 Apple TV 的新手，分层图像使用视差效果来生成三维效果，以帮助用户在床上记住与屏幕上的内容保持连接。

分层图像包含两（2）到五（5）个单独的层，它们组合在一起形成一个完整的映像。 除了背景层以外，每一层都使用其 Z 顺序和透明度来创建深度的错觉。 当用户与分层图像交互时，Z 顺序越大，Z 排序层就会进行缩放并重叠，以创建此效果。

[![](icons-images-images/layered01.png "Layered Images Z-ordered diagram")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> 应用的图标需要分层图像，对于其他可[设定焦点的项目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)（例如，上架图像）是可选的。 但是，Apple 建议对可在应用中获得焦点的任何图像使用分层映像。

Apple 为设计分层映像提供以下建议：

- **使背景层不透明**-您的背景层（第1层）**必须**不透明，否则在 Apple TV 上尝试使用分层图像时将会出现错误。 所有其他层都可以包含多个级别的透明度，以增强三维效果。
- **隔离前景、中间和背景元素**-将重要元素（例如游戏字符）放置在前台，并使用中间作为次要元素或阴影。 最后，请提供一个非特定背景，为上层提供一个阶段。
- 将**文本保留在前台**-除非你希望文本被更高级别遮盖，通常它应位于最顶层。
- **使用简单分层**-视差效果被设计为微妙，因此使层保持最小，以防止出现 jarring 和不切实际的影响。
- **包括安全区域**-因为在视差效果中可以裁剪上层，所以需要在每个层中构建一个安全区域边框。 如果你的内容过于接近层边缘，则会被裁剪掉。 上层会经历更多的缩放和裁剪。 请参阅下面的[大小调整图像层](#Sizing-Image-Layers)部分。
- **通常应预览**分层图像，以确保所需的三维效果发生，并且不会裁剪各个层上的任何内容。 你应在实际的 Apple TV 硬件上预览你的分层映像，以确保它们按预期方式呈现。

如果可能，应始终使用内置 `UIKit` 控件来显示分层图像，因为它们会在其进入焦点时自动获得视差效果。

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>调整图像层大小

务必要记住，将_安全区域_边框包含到将组成分层图像的每个层中。 由于可以在视差效果中缩放和裁剪各个层，因此，如果层的内容太近，则可以将它们裁剪掉：

[![](icons-images-images/layered02.png "35 pixel border")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>创建分层映像

tvOS 采用以下格式处理分层图像：

- **车载文件**-这是 Apple 创建的专有资产目录格式。 不会直接创建车载文件，它们是在编译时从任何 LSR 文件创建的，并且包含在应用程序捆绑包中。
- **LSR 映像**-这是由 Apple 创建的专用映像格式。 使用[视差导出程序 Adobe Photoshop 插件](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip)或[视差预览](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)器以 LSR 格式创建和预览分层图像。
- **Assets.xcassets** -在资产目录中包含的两个（2）到五（5）个标准 `.png` 格式的图像，将在编译时编译为汽车或 LSR 格式的分层图像。
- **LCR 文件**-这是 Apple 创建的专有文件格式。 LCR 文件旨在用作从一个内容服务器下载的附加内容。 在应用程序捆绑包中永远不应包含 LCR 文件。 使用 Xcode 随附的 `layerutil` 命令行工具从 LSR 或 Photoshop 文件生成 LCR 文件。

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>视差预览器

Apple 创建了[视差](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)预览器，用于预览和创建应用程序图标和可选可获得焦点的项所需的分层映像。 预览器会显示构成完成的分层映像的每个层：

[![](icons-images-images/layered03.png "The Parallax Previewer")](icons-images-images/layered03.png#lightbox)

预览分层图像时，可以使用鼠标旋转图像并预览视差效果。 使用 **+** （加号）和 **-** （减号）按钮添加和删除层。

创建新的分层映像时，可使用 LSR 格式导出该映像，并将其包含在应用的捆绑包中。

有关创建和预览分层图像的详细信息，请参阅[TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)的 Apple[创建视差图稿](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)部分。

<a name="App-Icons" />

## <a name="app-icons"></a>应用程序图标

你的 tvOS 应用不仅需要 Apple TV 主屏幕的应用图标，还需要应用商店的图标。 应用图标是您首次做出的更改，可让您的潜在用户印象非常好，并应一目了然地传达您的应用程序的用途。

[![](icons-images-images/icon01.png "The App Icon")](icons-images-images/icon01.png#lightbox)

每个应用都必须同时提供小版本的应用图标。 安装应用程序时，将在 Apple TV 主屏幕上使用小图标。 应用商店使用大版本。 大型应用图标应模拟小图标版本的外观。

|小图标||大图标||
|---|---|---|---|
|实际大小|400x240px|大小|1280x768px|
|安全区域大小|370x222px|||
|失去焦点大小|300x180px|||
|重点大小|370x222px|||

> [!IMPORTANT]
> 应用图标必须作为**分层映像**提供。 有关更多详细信息，请参阅上面的[分层图像](#Layered-Images)部分。

Apple 提供了以下有关创建应用图标的建议：

- **提供单个焦点**-设计图标时，会将一个焦点直接置于图标中心。
- **使用简单背景**-使您的图标背景简单，使高层显得更简单。请考虑使用简单的颜色或微妙的渐变。
- **限制文本量**–由于应用程序的名称将显示在用户选择的图标下，因此，只应在设计图标时包含文本。
- **不要使用屏幕快照**–屏幕截图太复杂，不允许用户一目了然地查看应用程序的用途。
- **保留图标正方形**– tvOS 会自动应用掩码，用于对图标的角进行细微的舍入。 请勿自行添加此舍入。
- **仔细分隔层**–文本应在最上层，第二项位于中间，而非特定背景，这允许您的上层。
- **小心使用渐变和阴影**–梯度和阴影可能会与视差效果冲突，因此应小心使用它们。 简单的从上到下、从淡到暗的渐变样式工作最佳。 阴影通常最适合作为清晰的硬边缘色调。
- **改变层透明度**–在应用的较高级别使用不同级别的透明度，以提高三维效果。 背景层必须不透明，否则将导致错误。

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>设置应用程序图标

若要设置 tvOS 项目所需的应用图标，请执行以下操作：

1. 在**解决方案资源管理器**中，双击 "`Assets.xcassets`" 将其打开进行编辑： 

    [![](icons-images-images/asset01.png "The Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. 在**资产编辑器**中，展开 "`App Icon & Top Shelf Image` 资产： 

    [![](icons-images-images/asset04.png "Expand the Top Shelf Image asset")](icons-images-images/asset04.png#lightbox)
3. 接下来，展开 `App Icon - Small` 资产： 

    [![](icons-images-images/asset05.png "Expand the App Icon - Small asset")](icons-images-images/asset05.png#lightbox)
4. 然后展开 `Back` 资产，并单击 `Contents` 条目： 

    [![](icons-images-images/asset06.png "Then expand the Back asset")](icons-images-images/asset06.png#lightbox)
5. 单击 " **1X APPLE TV" 条目**，并选择 "图像文件"。
6. 对 `Front` 和 `Middle` 资产重复上述步骤。
7. 然后重复相同的步骤来定义 `App Icon - Large` 资产。
8. 保存所做更改。

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>顶层图像

如果用户将 tvOS 应用放置在 Apple TV 主屏幕上的最上面一行，则当用户选择应用程序时，将显示一个大型的顶部图像。 此图像应突出显示应用的功能，或提供指向其内容的直接链接。

[![](icons-images-images/topshelf01.png "Top Shelf Image example")](icons-images-images/topshelf01.png#lightbox)

可以将顶架图像作为单个静态 `.png` 或 `.lsr` 文件提供（请参阅[创建分层映像](#Creating-Layered-Images)），也可以在运行时将其作为可设定焦点的单个行动态创建（请参阅下面的[动态上架内容](#Dynamic-Top-Shelf-Content)）。

|最大架映像大小|说明|
|---|---|
|1920x720px|静态 .png 或 lsr 文件|

Apple 提供了以下有关创建顶级映像的建议：

- **使用丰富静态图像**–如果应用未提供动态内容，则其靠上架的图像将无法获得焦点。 使用此映像可突出显示应用或品牌的功能。
- **链接到应用内容**–动态顶架布局提供用户在应用中找到最重要的内容的快速链接。 使用此区域提供快速链接，以启动应用并立即跳转到给定内容。
- **展示最新的内容**–丰富的顶部内容可将用户绘制到你的应用，并使其更易于使用。 使用此区域可展示排名最高或最新的内容。
- **个性化内容**-用户将最常用或最喜欢的应用放在主屏幕的最上面一行。 使用顶部货位显示他们最感兴趣的内容。
- **不允许广告**–完全禁止在顶部货位显示广告。 你可能会显示最新的可购买内容，但不会显示任何定价信息。

### <a name="setting-the-top-shelf-image"></a>设置上架图像

若要为 tvOS 项目设置所需的顶层映像，请执行以下操作：

1. 在**解决方案资源管理器**中，双击 "`Assets.xcassets`" 将其打开进行编辑： 

    [![](icons-images-images/asset01.png "The Assets.xcassets file")](icons-images-images/asset01.png#lightbox)
2. 在**资产编辑器**中，展开 "`App Icon & Top Shelf Image` 资产： 

    [![](icons-images-images/asset04.png "Expand the Top Shelf Image asset")](icons-images-images/asset04.png#lightbox)
3. 单击 "`Top Shelf Image` 资产： 

    [![](icons-images-images/asset07.png "The Top Shelf Image asset")](icons-images-images/asset07.png#lightbox)
4. 单击 " **1X APPLE TV" 条目**，并选择 "图像文件"。
5. 保存所做更改。

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>动态顶架内容

靠上架可以包含可[设定焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)或动态滚动条的滚动条，而不是显示静态顶部图像。 这两种动态样式都允许您突出显示应用程序提供的内容或跳转到其最常用的功能。

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>剖剖内容行

此动态顶架内容类型提供单个滚动行，可选择性地将其划分为多个部分。 它通常用于突出显示新的、最喜欢或最近查看的应用内容。

内容以单个水平滚动列表的形式显示，其中的标签显示在选定内容的当前片段下（当前有焦点）。 如果用户选择一段给定的内容，你的应用程序将会启动，并且应直接导入到该内容中。

需要以下内容大小：

||海报（2:3）|正方形（1:1）|HDTV （16:9）|
|---|---|---|---|
|实际大小|404x608px|608x608px|908x512px|
|安全区域大小|380x570px|570x570px|852x479px|
|失去焦点大小|333x500px|500x500px|782x440px|
|重点大小|380x570px|570x570px|852x479px|

Apple 为分段内容行提供以下建议：

- **完成行**–应提供足够的内容以跨越整个屏幕宽度。
- **缩放混合图像**–剖剖内容行旨在容纳混合图像大小（从上面提供的列表中）。 不过，如果你组合图像大小，请注意，将应用其他缩放以规范化内容显示。

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>滚动嵌入横幅

（可选）你的 tvOS 应用可以将其内容以自动滚动和循环显示几乎填满屏幕的横幅集合的形式显示在其顶部。 此样式通常用于展示丰富的新内容，例如新的电视节目。

除了自动滚动外，用户还可以控制横幅，并使用 Siri 远程的任一方向进行滚动。 当横幅处于焦点时，在 Siri 遥控器上发出一个小的循环手势将激活该横幅的视差效果。

**横幅图像（超大）**

|   |   |
|---|---|
|实际大小|1940x624px|
|安全区域大小|1740x620px|
|失去焦点大小|1740x560px|
|重点大小|1740x620px|

滚动嵌入横幅既可以作为静态 `.png` 提供，也可以作为分层 `.lsr` 文件提供。

Apple 提供以下有关滚动条带横幅的建议：

- **内容量**-你至少应提供三（3）个横幅，以使滚动外观合理。 你应包含不超过8个（8）个横幅，否则它会使最终用户的导航更困难。
- **内容文本**-如果横幅需要中的文本应包括在横幅图像中。 如果使用分层图像，则文本应位于最顶层。

有关将顶部货位扩展添加到应用的详细信息，请参阅 Apple 的[TVServices Framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)，以提供动态的顶层内容。

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Game Center 图像

如果你的 tvOS 应用是游戏，并且已包括 Game Center 支持，将需要多个图像资产：

- **成就图标**-将自动裁剪为圆形的每个成就都需要一个不透明的图像。 成就是不可设定焦点的项。
- **面板图稿**-可提供一个可选的图像，该图像将显示在应用程序仪表板顶部 Game Center 中。 这些映像不可获得焦点。
- **排行榜图稿**-你必须为应用支持的每个排行榜提供一（1）到3（3）16:9 纵横比映像。 它们可能是静态 `.png` 或分层 `.lsr` 文件。 排行榜图稿可获得焦点。

||成就图标|面板图稿|排行榜图稿|
|---|---|---|---|
|可见大小|200x200px|923x150px|不适用|
|实际大小|320x320px|不适用|659x371px|
|安全区域大小|不适用|不适用|618x348px|
|失去焦点大小|不适用|不适用|548x309px|
|重点大小|不适用|不适用|618x348px|

有关使用 Game Center 的详细信息，请参阅 Apple [Game Center 编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images" />

## <a name="working-with-images"></a>处理映像

由于 tvOS 9 是 iOS 9 的子集，因此用于在 Xamarin iOS 应用程序中包括和显示映像的技术也适用于 tvOS 应用。 有关详细信息，请参阅[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文档。

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>设置 tvOS 项目映像

如上所述，所有 tvOS 应用都需要一个[启动映像](#Launch-Image)和[应用图标](#App-Icons)。 本部分介绍在资产目录中设置 tvOS 应用项目后，为其选择 "启动映像" 和 "应用" 图标。

请执行以下操作：

1. 在**解决方案资源管理器**中，双击 `Info.plist` 将其打开进行编辑： 

    [![](icons-images-images/info01.png "The Info.plist file")](icons-images-images/info01.png#lightbox)
2. 在**Info.plist 编辑器**中，在 "**应用程序图标**" 的 "[设置应用程序图标](#Setting-the-App-Icons)" 部分中选择 "资产" 目录（前面已配置）： 

    [![](icons-images-images/info02.png "The Info.Plist Editor")](icons-images-images/info02.png#lightbox)
3. 接下来，在 "**启动**映像" 部分的 "[设置" "启动映像](#Setting-the-Launch-Image)" 部分中选择 "资产" 目录（前面已配置）。
4. 保存所做更改。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了 tvOS 应用程序中使用的所有映像类型和大小。 首先，它涵盖了启动图像、分层图像、应用图标、顶架图像和 Game Center 图像。 然后，它介绍了如何在 tvOS 应用程序中使用图像。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
