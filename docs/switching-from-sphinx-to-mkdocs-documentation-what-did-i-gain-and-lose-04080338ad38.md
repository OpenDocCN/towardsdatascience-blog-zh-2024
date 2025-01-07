# 从 Sphinx 切换到 MkDocs 文档 —— 我得到了什么，失去了什么？

> 原文：[`towardsdatascience.com/switching-from-sphinx-to-mkdocs-documentation-what-did-i-gain-and-lose-04080338ad38?source=collection_archive---------9-----------------------#2024-02-02`](https://towardsdatascience.com/switching-from-sphinx-to-mkdocs-documentation-what-did-i-gain-and-lose-04080338ad38?source=collection_archive---------9-----------------------#2024-02-02)

## 关于执行此切换的指南，以及它们之间的比较

[](https://kayjanwong.medium.com/?source=post_page---byline--04080338ad38--------------------------------)![Kay Jan Wong](https://kayjanwong.medium.com/?source=post_page---byline--04080338ad38--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--04080338ad38--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--04080338ad38--------------------------------) [Kay Jan Wong](https://kayjanwong.medium.com/?source=post_page---byline--04080338ad38--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--04080338ad38--------------------------------) ·11 分钟阅读·2024 年 2 月 2 日

--

![](img/1a331c257915b007b5798901def47ad5.png)

图片由[melanfolia](https://unsplash.com/@melanfolia?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

“*一个项目的好坏取决于它的文档*” —— 这是我之前的三部分文章系列中的一句名言，强调了文档的重要性。从那时起，我了解到文档有不同的种类，其中较为常见的包括：

1.  **技术文档**：描述项目内部运作的技术方面，可以是过程文档或产品文档

1.  **业务文档**：描述它解决了哪些业务问题或实现了哪些业务目标

> 存在一种类型的文档介于两者之间，它们是手册、指南和教程。特别是当你希望**技术用户**与项目合作或使用项目，或希望**业务用户**理解项目并获得支持时，这一点尤为重要。

随着文档内容的增加，文档变得越来越复杂，如何以**用户友好且易读的格式**展示文档可能会变得很棘手。例如，教程不应该嵌入到技术文档中，且……
