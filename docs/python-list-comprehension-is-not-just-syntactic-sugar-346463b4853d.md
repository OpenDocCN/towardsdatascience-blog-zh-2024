# Python 列表推导式不仅仅是语法糖

> 原文：[https://towardsdatascience.com/python-list-comprehension-is-not-just-syntactic-sugar-346463b4853d?source=collection_archive---------2-----------------------#2024-04-08](https://towardsdatascience.com/python-list-comprehension-is-not-just-syntactic-sugar-346463b4853d?source=collection_archive---------2-----------------------#2024-04-08)

![](../Images/1a0aa5b120483ab0b9b795e6d943d3d0.png)

作者创作的图片

## “Pythonic” 不是一个好的理由。为什么性能更好？什么时候不该使用它？

[](https://christophertao.medium.com/?source=post_page---byline--346463b4853d--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--346463b4853d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--346463b4853d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--346463b4853d--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--346463b4853d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--346463b4853d--------------------------------) ·阅读时间：8 分钟·2024 年 4 月 8 日

--

我猜你一定发现有很多文章告诉我们在 Python 中使用列表推导式而不是 for 循环。我看过很多这样的文章。然而，我有些惊讶的是，几乎没有文章解释为什么。

像我这样的人不会仅仅被“Pythonic”或“可读性”这样的理由说服。相反，这些“理由”实际上给 Python 新手留下了错误的印象。也就是说，Python 的列表推导式只是语法糖。

事实上，列表推导式是 Python 中的一项重要优化。在本文中，我们将深入探讨其背后的机制。你将得到以下问题的答案。

+   **什么是 Python 中的列表推导式？**

+   **为什么它的性能通常比 for 循环更好？**

+   **什么时候我们不应该使用列表推导式？**

# 1\. 简单的性能比较

![](../Images/2d532fdd4e3d92a296d79078bab966c2.png)

作者创作的图片
