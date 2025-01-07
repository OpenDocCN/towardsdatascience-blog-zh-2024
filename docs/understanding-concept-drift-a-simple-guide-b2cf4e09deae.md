# 理解概念漂移：一个简单的指南

> 原文：[`towardsdatascience.com/understanding-concept-drift-a-simple-guide-b2cf4e09deae?source=collection_archive---------8-----------------------#2024-03-05`](https://towardsdatascience.com/understanding-concept-drift-a-simple-guide-b2cf4e09deae?source=collection_archive---------8-----------------------#2024-03-05)

## 分布变化如何发生，以及验证延迟的影响。

[](https://vcerq.medium.com/?source=post_page---byline--b2cf4e09deae--------------------------------)![Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--b2cf4e09deae--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b2cf4e09deae--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2cf4e09deae--------------------------------) [Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--b2cf4e09deae--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2cf4e09deae--------------------------------) ·阅读时长 6 分钟·2024 年 3 月 5 日

--

![](img/96df4e7d55c6e2d7c54efe53f3a06817.png)

图片由[Rui Alves](https://unsplash.com/@asfotosde1enorme?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

概念漂移的检测与适应是 AI 系统监控的关键步骤。

在本文中，我们将：

+   描述什么是概念漂移，以及它如何在时间相关的数据中产生

+   探讨验证延迟及其对变化检测过程的影响

+   使用 scikit-multiflow 展示一个变化检测的例子

# 引言

机器学习模型隐含地假设数据是平稳的。它们期望测试或生产样本的分布与训练集中的分布相同。

然而，这一假设在展现时间相关结构的现实问题中几乎从未得到满足。

在现实世界环境中，数据的分布往往随着时间发生变化。这种变化被称为**概念漂移**，它发生在多个应用领域。在商业中，消费者对服务的兴趣可能会因为季节性变化或新兴趋势而发生改变。在金融领域，经济变化会导致消费或信贷条件的变化。
