# 使用 Markdown 改进 RAG 文档处理

> 原文：[https://towardsdatascience.com/improved-rag-document-processing-with-markdown-426a2e0dd82b?source=collection_archive---------0-----------------------#2024-11-19](https://towardsdatascience.com/improved-rag-document-processing-with-markdown-426a2e0dd82b?source=collection_archive---------0-----------------------#2024-11-19)

## 如何阅读和转换 PDF 为 Markdown，以便在 LLMs 中获得更好的 RAG 结果

[](https://medium.com/@leoneversberg?source=post_page---byline--426a2e0dd82b--------------------------------)[![Dr. Leon Eversberg](../Images/56dc3579a29933f7047a9ce60be4697a.png)](https://medium.com/@leoneversberg?source=post_page---byline--426a2e0dd82b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--426a2e0dd82b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--426a2e0dd82b--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--426a2e0dd82b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--426a2e0dd82b--------------------------------) ·10 分钟阅读·2024年11月19日

--

![](../Images/ac75eb85d3ffb2ce31541c2d36d448da.png)

图片来源：[insung yoon](https://unsplash.com/@insungpandora?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**Markdown** 是一种轻量级、易读的语言，用于创建**格式化文本**。许多人可能熟悉 GitHub 上的 README.md 文件，它就是使用 Markdown 编写的。

以下是一些 Markdown **语法**的基本示例：

```py
# Heading level 1
## Heading level 2
### Heading level 3

This is **bold text**.

This is *italicized text*.

> This text is a quote

This is how to do a link [Link Text](https://www.example.org)

```

这段文本是代码

```py

| Header 1   | Header 2   |
|------------|------------|
| table data | table data |
```

Markdown 似乎正在成为 **大型语言模型（LLMs）** 流行的格式。

Markdown 具有一些重要的优势，如 [1]：

+   它为标题、表格、列表、链接等提供了**结构**

+   它添加了 **排版强调** 元素，如粗体或斜体

+   它是**易于编写**且具有可读性的

+   它已经被**广泛使用**，例如在 GitHub 和 Jupyter notebooks 中

Markdown 不仅在 LLMs 的输入文档中有用，而且它也是像 **ChatGPT 这样的聊天机器人格式化其内容的方式**…
