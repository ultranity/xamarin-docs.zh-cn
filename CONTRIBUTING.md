---
ms.openlocfilehash: 0071fcc72844e4816e707c5828464859e09db68c
ms.sourcegitcommit: 2503da076966d12a8bdbf7af209662b74913a15c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78261613"
---
# <a name="contributing"></a>参与

感谢你对参与 Xamarin 文档的关注！

本页介绍在 [Xamarin 文档](https://docs.microsoft.com/xamarin)中更新内容的基本流程。

- [参与者许可协议](LICENSE)

## <a name="process-for-contributing"></a>参与流程

### <a name="small-changes--edits"></a>小规模更改和编辑

若要进行更正和执行小规模更新，可以单击任何页面的“编辑”按钮并在 GitHub 网站进行参与，还可以按照以下步骤执行  ：

1. 对 `MicrosoftDocs/xamarin-docs` 存储库进行分叉。

2. 针对更改创建 `branch`。

3. 编写内容。 请参阅[模板](contributing-guidelines/template.md)和[风格指南](contributing-guidelines/voice-tone.md)。

4. 从分支中将拉取请求 (PR) 提交到 `MicrosoftDocs/xamarin-docs/live`。

5. 通过 PR 与团队讨论后，对分支进行必要的更新。

6. 一旦反馈得到应用且更改看起来不错，维护人员将合并 PR。 它将很快出现在 docs.microsoft.com 上。

> [!NOTE]
> 如果 PR 正在解决现有问题，请将 `Fixes #Issue_Number` 关键字添加到提交消息或 PR 描述中，以便在合并 PR 时自动关闭该问题。 有关详细信息，请参阅[通过提交消息关闭问题](https://help.github.com/articles/closing-issues-via-commit-messages/)。

### <a name="big-changes-or-new-content"></a>重大更改或新增内容

对于执行重大更改和新增内容，请[提出问题](https://github.com/MicrosoftDocs/xamarin-docs/issues)，描述你要编写的文章及其与现有内容的相关性。 “文档”文件夹中的内容按产品领域（例如，Android 和 iOS）划分成不同的部分。 为新内容确定正确的文件夹。 

开始编写前，通过提问获得针对建议的反馈。 

如果要新建主题，可以使用[模板文件](../contributing-guidelines/template.md)作为起点。 它包含编写指南，还介绍了每篇文章所需的元数据，例如作者信息。

对于图像和其他静态资源，请将其添加到名为“\<mypage>-images”的子文件夹中  。 如果要为内容创建新文件夹，请将图像文件夹添加到新文件夹。

#### <a name="example-structure"></a>结构示例

```
docs
    /android
        mypage.md
        /mypage-images
            some-image.png
```

请务必遵循正确的 Markdown 语法。 有关详细信息，请参阅[风格指南](../contributing-guidelines/template.md)。

实际提交步骤与小规模更改的步骤相同（参见[上文](#process-for-contributing)）。

Xamarin 团队将审核 PR，并告知（通过 PR 反馈）更改是否看似不错或是否需要进行任何其他更新/更改才能获得批准。

一旦反馈得到应用且更改看起来不错，维护人员将合并 PR。

我们会以某种节奏，将所有提交内容从主分支推送到实时站点，你将能在 https://docs.microsoft.com/xamarin/ 中实时看到你参与编写的内容。

### <a name="contributing-to-international-content"></a>国际内容供稿

目前不接受机器翻译 (MT) 内容供稿。 为提高 MT 内容的质量，我们已转用一种神经 MT 引擎。 我们接受并鼓励人工翻译 (HT) 内容供稿，这些内容将用于训练神经 MT 引擎。 随着时间的推移，HT 内容供稿会同时提高 HT 和 MT 的质量。 MT 主题会包含一个免责申明，说明主题的部分内容可能为 MT，并且“编辑”按钮在处于禁用状态时不会显示  。

## <a name="dos-and-donts"></a>注意事项

下面是一个简短的指导规则列表，当你参与 .NET 文档时应牢记其中内容。

- 请勿发出大型拉取请求  。 可以提出问题并发起讨论，以便我们在你花费大量时间前确定方向。
- 请务必阅读[风格指南](contributing-guidelines/template.md)以及[语气和语调](contributing-guidelines/voice-tone.md)指南  。
- 请务必使用[模板](contributing-guidelines/template.md)文件作为工作的起点  。
- 请务必在处理文章前，在分叉上创建一个单独的分支  。
- 请务必遵循 [GitHub 流工作流](https://guides.github.com/introduction/flow/)  。
- 请务必在博客和推文（或任何社交软件上）频繁地发布你的参与内容  ！

> [!NOTE]
> 你或许会注意到某些主题目前并没有遵循此处指定的所有准则和[风格指南](contributing-guidelines/template.md)。 我们正努力实现整个站点的一致性。 
