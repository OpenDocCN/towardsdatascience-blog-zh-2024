# 如何（不）利用数据可视化作弊

> 原文：[https://towardsdatascience.com/how-not-to-cheat-with-data-visualizations-46fa9c8e13b0?source=collection_archive---------4-----------------------#2024-04-01](https://towardsdatascience.com/how-not-to-cheat-with-data-visualizations-46fa9c8e13b0?source=collection_archive---------4-----------------------#2024-04-01)

## 一份有些不同的指南……

[](https://medium.com/@michalszudejko?source=post_page---byline--46fa9c8e13b0--------------------------------)[![Michal Szudejko](../Images/d4c303d02a79ad29df193ed3b25910d9.png)](https://medium.com/@michalszudejko?source=post_page---byline--46fa9c8e13b0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--46fa9c8e13b0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--46fa9c8e13b0--------------------------------) [Michal Szudejko](https://medium.com/@michalszudejko?source=post_page---byline--46fa9c8e13b0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--46fa9c8e13b0--------------------------------) ·阅读时长17分钟·2024年4月1日

--

![](../Images/0345ffb0ef6be5b2bbb37e60d57d522d.png)

来源：作者通过ChatGPT生成的图像。

不久前，我发布了一篇关于使用KNIME进行数据可视化的文章。在开头的故事中，我以纪录片*《不愿面对的真相》*为例。电影展示了，包括复杂数据在内，如何通过可视化有效地呈现。电影中的数据、概念以及呈现的内容引发了许多争议和辩论。我的文章开头部分也引发了类似的讨论。

[](/data-visualization-with-knime-2b86548736d7?source=post_page-----46fa9c8e13b0--------------------------------) [## 使用KNIME进行数据可视化

### 一步步的全面指南

[towardsdatascience.com](/data-visualization-with-knime-2b86548736d7?source=post_page-----46fa9c8e13b0--------------------------------)

作者删除了我文章中的批评评论（或者至少我现在看不见它们了）。然而，这些批评启发我深入探索一个相对未被发掘的领域：创建具有误导性的数据显示方法的注意事项和禁忌。**本文正是旨在探讨这一话题，引导读者理解数据呈现中的完整性微妙之处。**

**在这篇文章中，我将介绍十四种你必须严格避免在演示工具包中使用的数据操控手法。如果你发现你的作品中藏有这些手法，你必须毫不犹豫地将其剔除……**
