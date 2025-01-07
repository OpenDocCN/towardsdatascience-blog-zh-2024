# 如何在BigQuery中运行引导分析

> 原文：[https://towardsdatascience.com/how-to-run-bootstrap-analysis-in-bigquery-029090b0bdcf?source=collection_archive---------9-----------------------#2024-02-18](https://towardsdatascience.com/how-to-run-bootstrap-analysis-in-bigquery-029090b0bdcf?source=collection_archive---------9-----------------------#2024-02-18)

## 通过引导你的数据，充分利用你手头的少量数据。

[](https://medium.com/@thomas.ellyatt?source=post_page---byline--029090b0bdcf--------------------------------)[![Tom Ellyatt](../Images/8756acdd11fef8db9a868820251e7575.png)](https://medium.com/@thomas.ellyatt?source=post_page---byline--029090b0bdcf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--029090b0bdcf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--029090b0bdcf--------------------------------) [Tom Ellyatt](https://medium.com/@thomas.ellyatt?source=post_page---byline--029090b0bdcf--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--029090b0bdcf--------------------------------) ·阅读时间11分钟·2024年2月18日

--

![](../Images/9d4728e1a5bc98f1855daacb9ac557c1.png)

必须配有带带子的靴子 | 通过DALLE创建

# 引言

想象一下，你正在尝试衡量一片广阔森林中所有树木的平均高度。逐棵测量显然不现实，因此你测量一个小样本，并使用这些测量值来估算整个森林的平均值。在统计学中，引导法就遵循类似的原理。

这涉及从你的数据中抽取一个小样本，并通过重复采样的方法，估算数据集的统计值（如均值、中位数或标准差）。这种技术使你能够通过小样本对总体进行推断，并以更高的信心水平得出结论。

**在本文中，我们将讨论：**

+   引导法的基本概念，究竟是什么？

+   如何在BigQuery中实现引导样本

+   一个实验，旨在理解结果如何随着样本大小的变化而变化，以及这与已知统计量的关系

+   一个你可以带走并自己使用的存储过程

# 引导法基础
