# 一种实用的算法效率方法

> 原文：[`towardsdatascience.com/a-practical-approach-to-algorithm-efficiency-377621dbc04e?source=collection_archive---------11-----------------------#2024-02-16`](https://towardsdatascience.com/a-practical-approach-to-algorithm-efficiency-377621dbc04e?source=collection_archive---------11-----------------------#2024-02-16)

![](img/3483c983a658b21d42e96f1573a5485e.png)

图片由[Julian Hochgesang](https://unsplash.com/@julianhochgesang?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 探索大 O 符号、运行时效率和算法可扩展性

[](https://medium.com/@marcellopoliti?source=post_page---byline--377621dbc04e--------------------------------)![Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--377621dbc04e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--377621dbc04e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--377621dbc04e--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--377621dbc04e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--377621dbc04e--------------------------------) ·8 分钟阅读·2024 年 2 月 16 日

--

在我的文章中，我常常强调一个事实，那就是即使在从事人工智能工作时，软件工程技能仍然至关重要，因为归根结底我们生产的是代码。同样，算法理论也不例外，因为**机器学习算法依然是算法，理解如何评估它们至关重要**。例如，**你知道 transformers 中的注意力机制是如何扩展的吗**？

我使用[Deepnote](https://deepnote.com/)运行脚本：这是一个基于云的笔记本，适合协作数据科学项目，也非常适合原型开发。

## 基本算法分析

当我们研究或开发一个算法时，**理解它所需要的时间和空间资源的数量**是至关重要的。只有这样，我们才能理解它的扩展性。一个排序算法可能在一个长度为 3 的数组的玩具案例中看起来最优，但当我们有一个长度为 10M 的数组时，它可能会非常低效。

因此，比较两种不同算法的性能并非易事。某些情况下，一种算法可能看起来更快，而在其他情况下，另一种算法可能更快；我们需要一种数学的、**正式的方法来正确评估**…
