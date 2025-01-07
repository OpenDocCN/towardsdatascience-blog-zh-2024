# 使用嵌入模型和大语言模型（LLMs）分析非结构化 PDF 数据

> 原文：[`towardsdatascience.com/analyzing-unstructured-pdf-data-w-embedding-models-and-llms-f83ae9a57c2b?source=collection_archive---------4-----------------------#2024-06-15`](https://towardsdatascience.com/analyzing-unstructured-pdf-data-w-embedding-models-and-llms-f83ae9a57c2b?source=collection_archive---------4-----------------------#2024-06-15)

## 如何将 PDF 转化为可操作的见解

[](https://medium.com/@justin.laughlin12?source=post_page---byline--f83ae9a57c2b--------------------------------)![Justin Laughlin](https://medium.com/@justin.laughlin12?source=post_page---byline--f83ae9a57c2b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f83ae9a57c2b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f83ae9a57c2b--------------------------------) [Justin Laughlin](https://medium.com/@justin.laughlin12?source=post_page---byline--f83ae9a57c2b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f83ae9a57c2b--------------------------------) ·阅读时长 8 分钟·2024 年 6 月 15 日

--

![](img/c01ac4dafe564a7df8a588cc9e784f81.png)

图片由作者提供

我们生活在一个数据极其杂乱的世界里，几乎每个数据科学家或分析师都知道这一点。几乎可以说，无论你开始哪个数据科学项目，整个项目最困难的部分都是获取、打包和清洗数据，以便能够提供有价值的见解。如今大多数公司和个人面临的问题并不一定是数据的获取（那方面数据很多），而是如何打包和清洗那些非结构化的、杂乱无章的数据。

以公共公司的 SEC 文件中的 PDF 为例。这些文件包含数百页，详细说明了重要的公司信息。此外，新的文件每季度和每年都会创建，从公司上市以来到最新的文件为止。这些文件包含的***数据***非常庞大，但却不是传统意义上的数据。这些文档包含表格、图表、摘要、解释等大量内容。分析这些文档的问题不是因为它们缺乏信息（当然，有时候确实如此），而是更大的问题在于找到一种**快速简便的方式**来提取对***个体用户***最重要的细节。最后这一点可能是最重要的：个体用户。对于我来说…
