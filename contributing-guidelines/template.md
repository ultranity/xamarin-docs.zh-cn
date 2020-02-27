---
title: 此文章的标题
description: 对本文的正确、完整描述。 此处的内容是 Microsoft Docs 系统使用搜索结果返回的内容，因此，这最好是对此文档文件的目标和内容的完整描述
keywords: keyword、list、for、internal、search
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: f26a786f3da909c7d948fe99b4b3a1423352ecbe
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77553571"
---
# <a name="metadata-and-markdown-template"></a>元数据和 Markdown 模板

此模板包含 Markdown 语法的示例以及有关设置元数据的指南。 若要充分利用此模板，必须同时查看**原始 Markdown**和**呈现的视图**（例如，原始 Markdown 显示元数据块，而呈现的视图不显示）。

创建 Markdown 文件时，应将此模板复制到新文件中，按下面的指定填写元数据，将上面的 H1 标题设置为本文的标题，并删除内容。

## <a name="metadata"></a>元数据

完整元数据块，其中包含一些注释，如下所示：

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

一些重要说明：

- 元数据元素的冒号 (:) 和值之间**必须**有空格。
- 如果可选元数据元素没有值，则将其删除（不要留空或使用“na”）。
- 值（例如标题）中的冒号会中断元数据解析器。 在此情况下，使用双引号将标题括起来（例如， `title: "Writing .NET Core console apps: An advanced step-by-step guide"`）。
- **title**：此标题将显示在搜索引擎结果中。 此标题不需要与 H1 标题相同，并且包含的字符数不超过 60 个。
- author  、manager  、ms.reviewer  ：作者字段应包含作者的 GitHub 用户名  ，而不是其别名。  另一方面，“manager”和“ms.reviewer”字段应包含 Microsoft 别名。 ms.reviewer 指定与文章或功能相关联的 PM/开发人员的姓名。
- **ms.assetid**：文章的 GUID，用于内部跟踪，例如商业智能 (BI)。 创建新的 Markdown 文件文件后，可从 [https://www.guidgenerator.com](https://www.guidgenerator.com) 获取 GUID。

## <a name="basic-markdown-gfm-and-special-characters"></a>基本 Markdown、 GFM 和特殊字符

支持所有基本 Markdown 和 GitHub Flavored Markdown (GFM)。 有关这些主题的更多信息，请参阅：

- [Markdown 基本语法](https://daringfireball.net/projects/markdown/syntax)
- [DocFX Flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)
- [GFM 文档](https://guides.github.com/features/mastering-markdown)

Markdown 使用特殊字符如 \*、\` 和 \# 进行格式化。 如果要在内容中包括一个特殊字符，必须执行以下两个操作之一：

- 在特殊字符前面放置一个反斜杠来“转义”它（例如，将 \* 写为 `\*`）
- 对字符使用 [HTML 实体代码](http://www.ascii.cl/htmlcodes.htm)（例如，将 &#42; 写为 `&#42;`）。

## <a name="file-name"></a>文件名

文件名使用以下规则：

- 只包含小写字母、数字和连字符。
- 不包括空格或标点字符。 在文件名中使用连字符分隔单词和数字。
- 使用具体的动作动词，例如 develop、buy、build、troubleshoot。 不使用带 -ing 的单词。
- 不使用小单词 - 不包含 a、and、the、in、or 等。
- 必须为 Markdown 格式，使用 .md 文件扩展名。
- 保持文件名简短。 它们是文章 URL 的一部分。

## <a name="headings"></a>标题

使用句式单词首字母大写。 以下单词的第一个字母始终为大写：

- 标题的首个单词。
- 标题中冒号后面的首个单词（例如，“How to: Sort an array”）。

标题使用 atx 样式，即在行的开头使用 1-6 个哈希字符 (#) 来表示标题，对应于 HTML 标题级别 H1 到 H6。 上面使用的是第一和第二级别标题示例。

主题中**必须**只有一个第一级别标题 (H1)，此标题显示为页面上的标题。

如果标题以 `#` 字符结束，则需要在末尾额外添加一个 `#` 字符，以便正确呈现此标题。 例如 `# Async Programming in F# #`。

第二级标题生成页面 TOC，显示在“In this article”部分的页面标题下方。

### <a name="third-level-heading"></a>第三级标题
#### <a name="fourth-level-heading"></a>第四级标题
##### <a name="fifth-level-heading"></a>第五级标题
###### <a name="sixth-level-heading"></a>第六级标题

## <a name="text-styling"></a>文本样式

_斜体_用于文件、文件夹、路径（对于较长的项，将其拆分到各自行）- 新术语 - URL（除非呈现为链接，链接为默认值）。

**粗体**用于 UI 元素。

## <a name="links"></a>“链接”

### <a name="internal-links"></a>内部链接

若要链接到同一 Markdown 文件中的标题（也称为定位链接），需要找出想要链接到的标题的 ID。 若要确认 ID，请查看呈现的文章的源，以查找 ID（例如， `id="blockquote"`），并使用 # + ID 进行链接（例如， `#blockquote`）。
ID 是基于标头文本自动生成的。 因此，例如，如果某个唯一部分名称为 `## Step 2`，则 ID 应类似于 `id="step-2"`。

- 示例：`[Chapter 1](#chapter-1)`

若要链接到同一个存储库中的 Markdown 文件，请使用[相对链接](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2)，文件名末尾包含“.md”。

- 示例：`[Readme file](../readme.md)`
- 示例：`[Welcome to .NET](../docs/welcome.md)`

若要链接到同一个存储库中的 Markdown 文件中的标头，请使用相对链接 + 哈希标记进行链接。

- 示例：`[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>外部链接

若要链接到外部文件，请使用完整的 URL 作为链接。

- 示例：`[GitHub](http://www.github.com)`

如果在 Markdown 文件中显示一个 URL，此 URL 将转换为可单击的链接。

- 示例：`http://www.github.com`

### <a name="links-to-apis"></a>链接到 API

生成系统具有一些扩展功能，使我们能够链接到 .NET API，而无需使用外部链接。
链接到 API 时，可以使用从源代码自动生成的 API 的唯一标识符 (UID)。

可以使用以下任一种语法：

1. Markdown 链接：`[link_text](xref:UID)`
2. 自动链接：`<xref:UID>`
3. 简写形式： `@UID`

- 示例：`@System.String`
- 示例：`[String class](xref:System.String)`

有关使用这一表示法的详细信息，请参阅 [Using cross reference](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference)（使用交叉引用）。

> 目前，没有查找 UID 的便捷方法。 查找某个 API 的 UID 的最好方法是在此存储库中搜索它：[docascode/coreapi](https://github.com/docascode/coreapi)。 我们正努力实现将来拥有更好的系统。

当 UID 包含特殊字符 \` 或 \# 时，UID 值需要分别使用 HTML 编码为 60% 和 %23，如下面的示例所示：

- 示例：@System.Threading.Tasks.Task\`1 变为 `@System.Threading.Tasks.Task%601`
- 示例：@System.Exception\#ctor 变为 `@System.Exception.%23ctor`

## <a name="lists"></a>列表

### <a name="ordered-lists"></a>有序列表

1. 这
1. 是
1. 一个
1. Ordered
1. 列表

#### <a name="ordered-list-with-an-embedded-list"></a>包含嵌套列表的有序列表

1. Here
1. comes
1. 一个
1. embedded
    1. Miss Scarlett
    1. Professor Plum
1. ordered
1. list

### <a name="unordered-lists"></a>无序列表

- 这
- is
- a
- 无序
- list

#### <a name="unordered-list-with-an-embedded-list"></a>包含嵌套列表的无序列表

- 这
- 无序
- list
  - Mrs. Peacock
  - Mr. Green
- 包含
- 其他
    1. Colonel Mustard
    1. Mrs. White
- 列表

## <a name="horizontal-rule"></a>水平标尺

---

## <a name="tables"></a>表

| 表        | 很           | 酷  |
| ------------- |:-------------:| -----:|
| 第 3 列是      | 右对齐 | $1600 |
| 第 2 列是      | 居中对齐      |   $12 |
| 第 1 列为默认对齐方式 | 左对齐     |    $1 |

可以使用 [Markdown 表生成器工具](http://www.tablesgenerator.com/markdown_tables)来更轻松地创建它们。

### <a name="inline-code-blocks-with-language-identifier"></a>具有语言标识符的内联代码块

使用三个反撇号 (\`\`\`) + 语言 ID，将特定于语言的颜色编码应用到代码块。 此处是 [GFM 语言 ID](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs) 的完整列表。

#### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>通用代码块

将三个反撇号 (&#96;&#96;&#96;) 用于通用代码块编码。

> 建议的方法是使用具有上一节中所述的语言标识符的代码块，以便确保在文档站点中突出显示正确的语法。 仅在需要时使用通用代码块。

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>内联代码

将反撇号 (&#96;) 用于 `inline code`。 将内联代码用于命令行命令、数据库表和列名称，以及语言关键字。

## <a name="blockquotes"></a>块引用

> 干旱至今已持续了一千万年，可怕的蜥蜴的统治早已结束。 在位于赤道的这片陆地上（某天会把这里当成非洲），生存竞争已经达到新高潮，而胜利者尚未出现。 在这片贫瘠而干旱的土地上，只有那些渺小或者敏捷或者凶残的生物才能茁壮成长，亦或是有希望生存下去。

## <a name="images"></a>图像

### <a name="static-image-or-animated-gif"></a>静态图像或动态 gif

```
![this is the alt text](images/dotnet.png)
```

![响应式设计](images/responsivedesign.gif)

### <a name="linked-image"></a>链接的图像

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>视频

### <a name="channel-9"></a>第 9 频道

使用此特殊 blockquote 语法：

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

第 9 频道视频 URL 应以 `https` 开头并以 `/player` 结尾，否则，它将嵌入整个页面，而不仅仅是视频。

### <a name="youtube"></a>YouTube

使用此特殊 blockquote 语法：

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

YouTube 视频应包含 `/embed/`（例如，按上面的格式），否则，它将尝试呈现整个页面，且可能不起作用。

## <a name="docsmicrosoft-extensions"></a>docs.microsoft 扩展

docs.microsoft 为 GitHub Flavored Markdown 提供了其他一些扩展。

### <a name="alerts"></a>警报

请务必使用以下警报格式，以便在文档站点中以正确格式呈现警报。 但是，GitHub 上的呈现引擎无法区分它们。

#### <a name="note"></a>说明

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>警告

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>提示

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>重要事项

```
> [!IMPORTANT]
> This is IMPORTANT
```

这些警报呈现如下：

![警报样式](images/alerts.png)

### <a name="buttons"></a>按钮

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

可在 [Intune 文档](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices)中查看按钮操作示例。

### <a name="selectors"></a>选择器

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

可在 [Intune 文档](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps)中查看选择器操作示例。

### <a name="step-by-steps"></a>按步执行

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

可在[高级威胁分析文档](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2)中查看按步执行示例。
