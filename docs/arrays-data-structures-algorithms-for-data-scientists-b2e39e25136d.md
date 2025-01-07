# 数组 — 数据科学家的数据结构与算法

> 原文：[https://towardsdatascience.com/arrays-data-structures-algorithms-for-data-scientists-b2e39e25136d?source=collection_archive---------4-----------------------#2024-10-07](https://towardsdatascience.com/arrays-data-structures-algorithms-for-data-scientists-b2e39e25136d?source=collection_archive---------4-----------------------#2024-10-07)

## 动态数组和静态数组在后台是如何工作的

[](https://medium.com/@egorhowell?source=post_page---byline--b2e39e25136d--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--b2e39e25136d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b2e39e25136d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b2e39e25136d--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--b2e39e25136d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2e39e25136d--------------------------------) ·阅读时长6分钟·2024年10月7日

--

![](../Images/b303752d5288c84a7b3e77e7b7621d91.png)

图片由 [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

作为数据科学家，我们很少会被问到类似 LeetCode 的问题，因此我们学习数据结构和算法的需求不如软件工程师那样迫切。

然而，能够编写高效的代码对于你的数据科学职业生涯是一个巨大的助推器。试想，如果你能成为一名既懂得实现机器学习模型，又理解写代码的最佳实践，同时对软件工程有一定了解并拥有相关知识的“数据科学家”会怎样？

你突然变得非常有价值，几乎成了市场上的独角兽。这就是为什么我开始学习数据结构与算法课程，并计划分享我所学到的内容。

本文将专门讨论数组、它们是如何在后台工作的以及它们的不同类型。

# 数据结构

数据结构是计算机内存储信息的一种便捷方式。正如 [维基百科](https://en.wikipedia.org/wiki/Data_structure) 所定义的：

> **数据结构**是一种数据组织和存储格式，通常选择它是为了高效访问数据。更准确地说，数据结构是一种…
