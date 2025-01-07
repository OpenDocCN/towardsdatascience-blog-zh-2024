# 介绍全新的 Anthropic PDF 处理 API

> 原文：[`towardsdatascience.com/introducing-the-new-anthropic-pdf-processing-api-0010657f595f?source=collection_archive---------0-----------------------#2024-11-27`](https://towardsdatascience.com/introducing-the-new-anthropic-pdf-processing-api-0010657f595f?source=collection_archive---------0-----------------------#2024-11-27)

![](img/e659afb72bb244af875d4665e20cec0f.png)

图片由 AI（Dalle-3）生成

## Anthropic Claude 3.5 现在支持理解 PDF 输入

[](https://medium.com/@thomas_reid?source=post_page---byline--0010657f595f--------------------------------)![托马斯·里德](https://medium.com/@thomas_reid?source=post_page---byline--0010657f595f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0010657f595f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0010657f595f--------------------------------) [托马斯·里德](https://medium.com/@thomas_reid?source=post_page---byline--0010657f595f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0010657f595f--------------------------------) ·阅读时间 8 分钟·2024 年 11 月 27 日

--

在过去的几周里，Anthropic 发布了一些令人兴奋的 beta 功能，这些功能大多未受到关注。其中之一是它的新 token 计数 API。我已经写了一篇关于这个的文章，你可以通过点击下面的链接阅读。

[](/introducing-the-new-anthropic-token-counting-api-5afd58bad5ff?source=post_page-----0010657f595f--------------------------------) ## 介绍全新的 Anthropic Token 计数 API

### 使用 Claude 时，更加密切地关注成本

towardsdatascience.com

另一个令人兴奋的功能，也是本文的主题，是 Claude 3.5 现在可以处理 PDF，并理解 PDF 文档中的文本和视觉内容。

## PDF 功能

Claude 支持任何标准 PDF 文件，允许你查询文档中的文本、图片、图表和表格。以下是一些常见的使用案例：

+   分析财务报告，解读图表和表格

+   从法律文档中提取关键信息

+   协助文档翻译

+   将文档内容转换为结构化格式

## 限制
