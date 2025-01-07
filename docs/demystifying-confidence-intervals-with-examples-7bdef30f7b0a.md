# 通过示例揭秘置信区间

> 原文：[https://towardsdatascience.com/demystifying-confidence-intervals-with-examples-7bdef30f7b0a?source=collection_archive---------2-----------------------#2024-01-13](https://towardsdatascience.com/demystifying-confidence-intervals-with-examples-7bdef30f7b0a?source=collection_archive---------2-----------------------#2024-01-13)

## 在数据中驾驭不确定性以提取全局统计洞察

[](https://medium.com/@slavahead?source=post_page---byline--7bdef30f7b0a--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--7bdef30f7b0a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7bdef30f7b0a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7bdef30f7b0a--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--7bdef30f7b0a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7bdef30f7b0a--------------------------------) ·10分钟阅读·2024年1月13日

--

![](../Images/253db717de126c20ef60c2038c4764ce.png)

# 引言

**置信区间**是统计学中最重要的概念之一。在数据科学中，我们常常需要为给定的数据变量计算统计量。我们常遇到的问题是缺乏完整的数据分布。因此，统计量仅针对数据的一个子集进行计算。显而易见的缺点是，基于这个数据子集计算出的统计量可能与所有可能值的真实值相差很大。

![](../Images/54047bd8daf8ae95106aba8cad1e8934.png)

仅根据数据子集计算平均值的示例。当只有部分数据时，计算出的统计量可能与真实的总体值有显著差异。

完全消除这个问题是不可能的，因为我们总是会与真实值有所偏差。然而，引入置信区间并结合多个算法，可以估算出**在某一置信水平下，期望统计量所属的值范围**。

# 定义

在理解了置信区间的最终动机之后，让我们非正式地了解它们的含义：

> 对于给定的数字…
