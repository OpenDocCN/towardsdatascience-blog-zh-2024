# 理解交点（链条、分叉和碰撞器）及其在因果推断中的作用

> 原文：[`towardsdatascience.com/understanding-junctions-chains-forks-and-colliders-and-the-role-they-play-in-causal-inference-211db6770e2f?source=collection_archive---------7-----------------------#2024-01-31`](https://towardsdatascience.com/understanding-junctions-chains-forks-and-colliders-and-the-role-they-play-in-causal-inference-211db6770e2f?source=collection_archive---------7-----------------------#2024-01-31)

## 通过相关性、独立性和回归来解释交点，从而理解它们在因果推断中的关键重要性

[](https://grahamharrison-86487.medium.com/?source=post_page---byline--211db6770e2f--------------------------------)![Graham Harrison](https://grahamharrison-86487.medium.com/?source=post_page---byline--211db6770e2f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--211db6770e2f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--211db6770e2f--------------------------------) [Graham Harrison](https://grahamharrison-86487.medium.com/?source=post_page---byline--211db6770e2f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--211db6770e2f--------------------------------) ·阅读时间：25 分钟·2024 年 1 月 31 日

--

![](img/aaa98ea140f0391773854c84544eb5cc.png)

图片由[Ricardo Gomez Angel](https://unsplash.com/@rgaleriacom?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/stainless-steel-and-red-industrial-machine-41X6FwTwPh4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

因果推断是应用概率、可视化和机器学习来理解“为什么”这一问题的答案。

这是一个相对较新的数据科学领域，它有可能扩展预测算法的益处，预测算法解决的是潜在商业问题的症状，而通过确立因果关系，它可以永久性地治愈这些商业问题。

通常，因果推断会从数据集开始（像数据科学的任何其他分支一样），然后通过数据项之间关系的因果和效应的可视化表示来增强数据。常见的可视化形式是有向无环图（DAG）。

# 问题

有向无环图（DAG）看似简单，但它们隐藏了许多复杂性，必须充分理解这些复杂性，才能最大化因果推断技术的应用。

# 机会
