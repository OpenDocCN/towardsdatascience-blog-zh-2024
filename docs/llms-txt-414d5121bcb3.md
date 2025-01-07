# LLMs.txt 解析

> 原文：[`towardsdatascience.com/llms-txt-414d5121bcb3?source=collection_archive---------1-----------------------#2024-11-28`](https://towardsdatascience.com/llms-txt-414d5121bcb3?source=collection_archive---------1-----------------------#2024-11-28)

## 您的网络新标准：LLM 友好内容指南

[](https://medium.com/@dericksozo?source=post_page---byline--414d5121bcb3--------------------------------)![Derick Ruiz](https://medium.com/@dericksozo?source=post_page---byline--414d5121bcb3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--414d5121bcb3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--414d5121bcb3--------------------------------) [Derick Ruiz](https://medium.com/@dericksozo?source=post_page---byline--414d5121bcb3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--414d5121bcb3--------------------------------) ·阅读时长 5 分钟·2024 年 11 月 28 日

--

最近，您可能已经看到一些开发工具将 LLMs.txt 支持添加到它们的文档中。这一提议的网络标准正在迅速获得广泛采用，但它到底是什么，又为什么重要呢？

虽然 robots.txt 和 sitemap.xml 是为搜索引擎设计的，但 LLMs.txt 则是为推理引擎优化的。它以 LLM 易于理解的格式，提供有关网站的信息。

那么，LLMs.txt 是如何在几乎一夜之间，从提议到成为行业趋势的呢？

![](img/e1ff5a8aa4a2a4925545d7aa78cff2f4.png)

LLMs.txt 解析（图片来源：[Jørgen Larsen](https://unsplash.com/@px7digital?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)于[Unsplash](https://unsplash.com/photos/a-close-up-of-a-piece-of-paper-with-numbers-on-it-00PCjphxzpo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

# Mintlify 是如何推广 LLMs.txt 的

11 月 14 日，Mintlify 在其文档平台上添加了对 LLMs.txt 的支持。通过这一举措，他们使成千上万的开发工具文档对 LLM（大语言模型）更加友好，像是 Anthropic 和 Cursor 等工具。

Anthropic 和其他公司迅速在 X 平台上发布了关于其 LLMs.txt 支持的消息。更多由 Mintlify 托管的文档加入了这一行列，为这一提议的标准带来了广泛的关注。

这一势头激发了新的社区网站和工具的诞生。@ifox 创建了[directory.llmstxt.cloud](https://directory.llmstxt.cloud)来索引 LLM 友好的技术文档。@screenfluent 随后也发布了[llmstxt.directory](https://llmstxt.directory/)。

制作 dotenvx 的 Mot，构建并分享了一个[开源生成工具](https://github.com/dotenvx/llmstxt)用于 dotenvx 文档站点。Firecrawl 的 Eric Ciarla 创建了[一个工具](https://llmstxt.firecrawl.dev/)，该工具会抓取您的网站并为您生成 LLMs.txt 文件。

Anthropic 的 Claude 关系负责人 Alex Albert 在 X 平台上宣布 LLMs.txt 的支持。

# 谁创建了 LLMs.txt，为什么？

Answer.AI 的联合创始人 Jeremy Howard 提出了 LLMs.txt，以解决特定的技术挑战。

AI 系统只能处理有限的上下文窗口，这使得它们很难理解大型文档网站。传统的 SEO 技术是为搜索爬虫而优化的，而不是为推理引擎优化，因此无法解决这一限制。

当 AI 系统尝试直接处理 HTML 页面时，它们会被导航元素、JavaScript、CSS 和其他不必要的信息拖慢，这减少了实际内容可以占用的空间。

LLMs.txt 通过以 AI 理解的格式提供确切的信息来解决这个问题。

Jeremy Howard 的 X 个人资料，他是 LLMs.txt 网页标准提案的创建者。

# LLMs.txt 文件到底是什么？

LLMs.txt 是一个具有特定结构的 Markdown 文件。该规范定义了两个不同的文件：

1.  `/llms.txt`：简化版文档导航视图，帮助 AI 系统快速理解你网站的结构。

1.  `/llms-full.txt`：一个包含所有文档内容的综合文件。

## `/llms.txt`

该文件必须以 H1 项目名称开始，接着是一个引用摘要。后续部分使用 H2 标题来组织文档链接。“可选”部分专门标记不那么关键的资源。

```py
# Project Name
> Brief project summary

Additional context and important notes

## Core Documentation
- Quick Start: Description of the resource
- API Reference: API documentation details

## Optional
- Additional Resources: Supplementary information
```

例如，查看 llmtxt.org 的[LLM.txt](https://llmstxt.org/llms.txt)。如需更深入的多语言示例，请查看[Anthropic 的](https://docs.anthropic.com/llms.txt)。

## `/llms-full.txt`

虽然`/llms.txt`提供了导航和结构，但`/llms-full.txt`包含完整的文档内容，采用 Markdown 格式。

```py
# AI Review (Beta)

AI Review is a feature that allows you to review your recent changes in your codebase to catch any potential bugs.

<Frame>
  <img src="https://mintlify.s3-us-west-1.amazonaws.com/cursor/images/advanced/review.png" alt="AI Review" />
</Frame>

You can click into individual review items to see the full context in the editor, and chat with the AI to get more information.

### Custom Review Instructions

In order for AI Review to work in your favor, you can provide custom instructions for the AI to focus on. For example,
if you want the AI to focus on performance-related issues, you could put:

```

关注我的代码性能。

```py

This way, AI Review will focus on the performance of your code when scanning through your changes.

### Review Options

Currently, you have a several options to choose from to review:

*   `Review Working State`
    *   This will review your uncommitted changes.
*   `Review Diff with Main Branch`
    *   This will review the diff between your current working state and the main branch.
*   `Review Last Commit`
    *   This will review the last commit you made.
```

上述代码片段来自 Cursor 的`/llms-full.txt`文件。查看[Cursor 文档](https://docs.cursor.com/llms-full.txt)中的完整文件。

# LLMs.txt 与 sitemap.xml 和 robots.txt 的区别

它的作用与现有的网页标准（如 sitemap.xml 和 robots.txt）截然不同。

`/sitemap.xml`列出了所有可索引的页面，但不帮助内容处理。AI 系统仍然需要解析复杂的 HTML，并处理额外的信息，这会占用上下文窗口。

`/robots.txt`建议搜索引擎爬虫访问，但也无法帮助理解内容。

`/llms.txt`解决了与 AI 相关的挑战。它有助于克服上下文窗口限制，去除不必要的标记和脚本，并以优化 AI 处理的结构呈现内容。

# 如何将 LLMs.txt 与 AI 系统一起使用

与主动爬行网络的搜索引擎不同，当前的 LLM 并不会自动发现和索引 LLMs.txt 文件。

你必须手动将文件内容提供给 AI 系统。这可以通过粘贴链接、直接将文件内容复制到提示中，或使用 AI 工具的文件上传功能来完成。

## ChatGPT

首先，访问该文档或`/llms-full.txt`的 URL。将内容或 URL 复制到你的聊天中，提出你想要完成的具体问题。

![](img/3e331802fe46d82ba691553939fafa70.png)

使用 llms-full.txt 文件与 ChatGPT 结合的截图（图片来自作者）。

# Claude

Claude 目前还无法浏览网页，因此请将该文档的`/llms-full.txt`文件内容复制到剪贴板。或者，你可以将其保存为`.txt`文件并上传。现在你可以放心地提问，确保它包含完整且最新的上下文。

![](img/a98383169dcad3130025e2b734c9e61d.png)

使用 llms-full.txt 文件与 Claude 结合的截图（图片来自作者）。

# Cursor

Cursor 允许你添加和索引第三方文档，并在聊天中作为上下文使用。你可以通过输入@Docs > Add new doc 来做到这一点。一个弹出框会出现，在这里你可以添加指向`/llms-full.txt`文件的链接。你将能够像使用其他文档一样将其作为上下文使用。

要了解更多关于此功能的信息，请参见 Cursor 的[@Docs 功能](https://docs.cursor.com/context/@-symbols/@-docs)。

![](img/ec4774cc97ac951d797cf1f3e5d5fbe2.png)

输入 llms-full.txt 文件到 Cursor 中以用作上下文的截图（图片来自作者）。

# 如何生成 LLMs.txt 文件

有几种不同的工具可以用来创建你自己的文件：

+   [**Mintlify**](https://mintlify.com/): 自动生成托管文档的/llms.txt 和/llms-full.txt 文件。

+   [**dotenv 的 llmstxt**](https://github.com/dotenvx/llmstxt)：由 dotenvx 的创始人 Mot 开发的工具，可以通过你网站的`sitemap.xml`生成`llms.txt`文件。

+   [**Firecrawl 的 llmstxt**](https://llmstxt.firecrawl.dev/): 由 Firecrawl 的创始人 Eric Ciarla 开发的另一款工具，使用 Firecrawl 抓取你的网站并生成`llms.txt`文件。

# LLMs.txt 的未来是什么？

LLMs.txt 代表了向 AI 优先文档转型的趋势。

正如 SEO 对于搜索引擎的可见性至关重要一样，拥有 AI 可读的内容将成为开发工具和文档的关键。

随着更多网站采用这个文件，我们可能会看到新的工具和最佳实践出现，使内容对人类和 AI 助手都能访问。

目前，LLMs.txt 提供了一种实际的解决方案，帮助 AI 系统更好地理解和利用网页内容，特别是针对技术文档和 API。
