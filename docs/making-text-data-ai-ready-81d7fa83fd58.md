# 让文本数据准备好供AI使用

> 原文：[https://towardsdatascience.com/making-text-data-ai-ready-81d7fa83fd58?source=collection_archive---------5-----------------------#2024-10-04](https://towardsdatascience.com/making-text-data-ai-ready-81d7fa83fd58?source=collection_archive---------5-----------------------#2024-10-04)

## 使用无代码解决方案的介绍

[](https://b-r-i-a-n.medium.com/?source=post_page---byline--81d7fa83fd58--------------------------------)[![Brian Perron, PhD](../Images/8f9056642a4fd5e334a92dded0278961.png)](https://b-r-i-a-n.medium.com/?source=post_page---byline--81d7fa83fd58--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--81d7fa83fd58--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--81d7fa83fd58--------------------------------) [Brian Perron, PhD](https://b-r-i-a-n.medium.com/?source=post_page---byline--81d7fa83fd58--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--81d7fa83fd58--------------------------------) ·7分钟阅读·2024年10月4日

--

![](../Images/39e3750dd6343b524659c199b0412200.png)

展示处理杂乱数据的图形。图片由作者使用ChatGPT-4o制作。

人们使用大型语言模型在来自不同来源的文本数据上执行各种任务。这些任务可能包括（但不限于）编辑、总结、翻译或文本提取。这个工作流程的主要挑战之一是确保你的数据是AI-ready。本文简要概述了AI-ready的含义，并提供了一些无代码解决方案，帮助你达成这一目标。

# 什么是AI-ready？

我们被来自不同来源的大量非结构化文本数据包围，包括网页、PDF、电子邮件、组织文档等。在AI时代，这些非结构化文本文件可能是重要的信息来源。对于许多人来说，处理非结构化文本数据的典型工作流程是将一段文本提交给大型语言模型（LLM）。

![](../Images/a4e006bb050d0b768d12feb7b3946b1d.png)

ChatGPT中的翻译任务图像。截图由作者提供。

虽然复制粘贴方法是与LLM合作的标准策略，但你可能会遇到这种方法无法奏效的情况。考虑以下几点：

+   虽然许多高级模型允许上传并处理文档，但文件大小有限制。如果文件太大，你需要使用其他策略将相关文本输入到模型中。

+   你可能只想处理较大文档中的一小部分文本。将整个文档提供给LLM可能会因为包含无关文本而干扰任务的完成。

+   一些文本文件和网页，尤其是PDF文件，包含大量格式化内容，可能会干扰文本的处理方式。由于文档的格式问题，你可能无法使用复制粘贴方法——例如，表格和列的处理可能会出现问题。

"AI准备就绪"意味着你的数据格式可以被大型语言模型（LLM）轻松读取和处理。对于文本数据处理，数据是纯文本格式，并带有LLM可以轻松理解的格式。Markdown文件类型非常适合确保你的数据是AI准备就绪的。

# 普通文本与Markdown

普通文本是计算机上最基本的文件类型，通常用*.txt*扩展名表示。许多不同的_编辑器_可以用来创建和编辑纯文本文件，就像使用Microsoft Word创建和编辑格式化文档一样。例如，PC上的记事本应用程序或Mac上的TextEdit应用程序都是默认的文本编辑器。然而，与Microsoft Word不同，纯文本文件不允许你对文本进行格式化（例如，加粗、下划线、斜体等）。它们仅包含以纯文本格式表示的原始字符。

Markdown 文件是带有扩展名*.md*的纯文本文件。使Markdown文件独特的是，它使用某些字符来表示格式化。这些特殊字符会被支持Markdown的应用程序解析，并以特定的样式和结构呈现文本。例如，文本两侧加上星号会使其变为斜体，而双星号则会使文本加粗。Markdown还提供了简单的方法来创建标题、列表、链接和其他标准文档元素，同时保持文件为纯文本格式。

Markdown与大型语言模型（LLM）之间的关系是直接的。Markdown文件包含纯文本内容，LLM可以快速处理和理解。LLM能够识别并解释Markdown格式作为有意义的信息，从而提高文本理解。Markdown使用井号（#）表示标题，从而创建层级结构。一个井号表示一级标题，两个井号表示二级标题，三个井号表示三级标题，以此类推。这些标题在处理信息时为LLM提供了上下文提示。模型可以利用这一结构，更好地理解文本中不同部分的组织和重要性。

通过识别 Markdown 元素，LLM 可以理解内容及其预期的结构和重点。这导致更准确的文本解读和生成。这种关系使得 LLM 能够从文本的结构中提取额外的意义，不仅仅是从词汇本身，从而增强它们理解和处理 Markdown 格式文档的能力。此外，LLM 通常会以 Markdown 格式显示其输出。因此，通过提交和接收 Markdown 内容，您可以在与 LLM 工作时拥有更加简洁的工作流程。你还会发现，许多其他应用程序也支持 Markdown 格式（例如 Slack、Discord、GitHub、Google Docs）。

互联网上有许多资源可以学习 Markdown。以下是一些有价值的资源。请花些时间学习 Markdown 格式化。

+   [Markdown 教程](https://www.markdowntutorial.com/)

+   [Common Mark 教程](https://commonmark.org/help/tutorial/)

+   [Markdown 指南](https://www.markdownguide.org/)

# 必要工具

本节将探讨用于管理 Markdown 和将其与大型语言模型（LLM）集成的必要工具。工作流程包括几个关键步骤：

1.  源材料：我们从结构化的文本源开始，如 PDF、网页或 Word 文档。

1.  转换：通过使用专门的工具，我们将这些格式化文本转换为纯文本，具体来说是 Markdown 格式。

1.  存储（可选）：转换后的 Markdown 文本可以以其原始形式存储。如果您以后会重新使用或引用这些文本，建议执行此步骤。

1.  LLM 处理：Markdown 文本随后被输入到 LLM 中。

1.  输出生成：LLM 处理数据并生成输出文本。

1.  结果存储：LLM 的输出可以存储以供进一步使用或分析。

![](../Images/28bf3d81bb7f2c241dac48276c07a537.png)

将格式化文本转换为纯文本的工作流程。图像由作者使用 Mermaid 图表制作。

这个工作流程高效地将各种文档类型转换为 LLM 可以快速处理的格式，同时保留了存储输入和输出以供将来参考的选项。

## Obsidian：保存和存储纯文本

Obsidian 是保存和存储纯文本和 Markdown 文件的最佳选择之一。当我从 PDF 和网页中提取纯文本内容时，通常会将其保存在 Obsidian 中，Obsidian 是一个免费的文本编辑器，特别适合这个目的。我还使用 Obsidian 处理其他工作，包括做笔记和保存提示。这个工具非常棒，值得学习。

> Obsidian 仅仅是一个用于保存和存储纯文本内容的工具。你可能希望在工作流程中使用这一部分，但它**不是必需的**！

## Jina AI — 阅读器：从网站提取纯文本

Jina AI 是我最喜欢的 AI 公司之一。它提供了一套用于与 LLMs 合作的工具。Jina AI Reader 是一款出色的工具，它将网页转换为 markdown 格式，允许你提取纯文本内容，以便 LLM 处理。这个过程非常简单。只需在任何 URL 前添加 [https://r.jina.ai/](https://r.jina.ai/`)，你就能获得适合 LLM 的 AI 内容。

例如，考虑以下关于维基百科上大语言模型的截图：[en.wikipedia.org/wiki/Large_language_model](http://en.wikipedia.org/wiki/Large_language_model)

![](../Images/93648b5536dedae2384d292abe927370.png)

维基百科页面的截图由作者提供。

假设我们只想使用此页面中关于大语言模型（LLM）的文本。提取这些信息可以使用复制粘贴的方法，但这样会因为其他格式而显得繁琐。不过，我们可以通过将 `[https://r.jina.ai`](https://r.jina.ai`) 添加到 URL 的开头，使用 Jina AI-Reader：

+   原始 URL：[en.wikipedia.org/wiki/Large_language_model](http://en.wikipedia.org/wiki/Large_language_model)

+   使用 Jina 的 URL：[https://r.jina.ai/en.wikipedia.org/wiki/Large_language_model`](https://r.jina.ai/en.wikipedia.org/wiki/Large_language_model`)

这将以 markdown 格式返回所有内容：

![](../Images/be2be5242d73c0cf88d86e7daacc7fdf.png)

维基百科页面通过 Jina AI-Reader 转换为 markdown 格式。图片由作者提供。

在这里，我们可以轻松地将相关内容复制粘贴到 LLM 中。或者，我们可以将 markdown 内容保存在 Obsidian 中，便于长期重复使用。虽然 Jina AI 提供的高级服务费用非常低，但你仍然可以免费使用这个工具。

## LlamaParse：从文档中提取纯文本

高度格式化的 PDF 和其他样式化文档呈现了另一种常见挑战。在处理大语言模型（LLM）时，我们通常需要去除格式，专注于内容。考虑一个场景，你只想使用 PDF 报告中的特定部分。文档的复杂样式使得简单的复制粘贴变得不现实。此外，如果你将整个文档上传到 LLM，它可能会难以准确找到并处理所需的部分。这种情况需要一个可以将内容与格式分离的工具。LlamaIndex 的 LlamaParse 能够有效地解耦文本和样式元素，满足这一需求。

要访问 LlamaParse，你可以登录 LlamaCloud：[https://cloud.llamaindex.ai/login](https://cloud.llamaindex.ai/login)。登录 LlamaCloud 后，前往屏幕左侧的 LlamaParse：

![](../Images/ff17e3d369fb8aab7f312e0451fa12f6.png)

LlamaCloud 截图。图片由作者提供。

在访问了解析功能后，您可以按照以下步骤提取内容。首先，将模式更改为“准确”，该模式会生成markdown格式的输出。其次，拖放您的文档。您可以解析许多不同类型的文档，但根据我的经验，您通常需要解析PDF、Word文件和PowerPoint文件。只要记住，您可以处理许多不同类型的文件。在这个例子中，我使用了[美国社会工作委员会的公开报告](https://www.aswb.org/wp-content/uploads/2022/07/2022-ASWB-Exam-Pass-Rate-Analysis.pdf)。这是一个具有高度设计风格的报告，共94页。

![](../Images/67b52f20a3bf3694c59f801834ce3f7f.png)

LlamaCloud的截图。图像来自作者。

现在，您可以复制并粘贴markdown内容，或者将整个文件导出为markdown格式。

![](../Images/53eef0cf0baca7b739fb7c6f5031ad17.png)

LlamaParse输出的截图。图像来自作者。

在免费计划下，您每天可以解析1,000页。LlamaParse还有许多其他值得探索的功能。

# 最后的想法

准备文本数据以进行AI分析涉及多种策略。尽管一开始使用这些技巧可能看起来有些挑战，但通过练习，您将变得更加熟悉这些工具和工作流程。随着时间的推移，您将学会高效地将它们应用到具体任务中。
