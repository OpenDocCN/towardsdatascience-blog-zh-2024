# 使用 Python 实现凝聚型层次聚类

> 原文：[`towardsdatascience.com/implement-agglomerative-hierarchical-clustering-with-python-e2d82dc69eeb?source=collection_archive---------7-----------------------#2024-01-18`](https://towardsdatascience.com/implement-agglomerative-hierarchical-clustering-with-python-e2d82dc69eeb?source=collection_archive---------7-----------------------#2024-01-18)

## 无监督学习

## 在这篇文章中，我简要介绍了无监督学习方法、层次聚类的概念以及其在 Python 中的实现。

[](https://jianan-lin.medium.com/?source=post_page---byline--e2d82dc69eeb--------------------------------)![Yufeng](https://jianan-lin.medium.com/?source=post_page---byline--e2d82dc69eeb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2d82dc69eeb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2d82dc69eeb--------------------------------) [Yufeng](https://jianan-lin.medium.com/?source=post_page---byline--e2d82dc69eeb--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2d82dc69eeb--------------------------------) ·阅读时间 9 分钟·2024 年 1 月 18 日

--

![](img/80bc176dcb160313eccc0f50bbf0a290.png)

图片来源：[Bee Balogun](https://unsplash.com/@bee_balogun?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

层次聚类是统计学习中最基本的聚类方法之一。如果你的数据集不大，并且你希望不仅看到每个数据点的聚类标签，还能看到整个数据的内部结构，层次聚类是一个很好的起点。

为了说明清楚，层次聚类方法有两种类型，*凝聚型*和*分裂型*聚类。

唯一的算法设计差异是*聚类过程的方向*。

***凝聚型***聚类是一种***自下而上***的方法，其中每个数据点一开始都是一个单独的聚类，然后*迭代地合并*成更大的聚类；

相反，***分裂型***聚类是一种***自上而下***的方法，在开始时整个数据集是一个单一的聚类，随着过程的进行，较大的聚类会*递归地分裂*。

由于凝聚型聚类在表示层次聚类时更为流行，因此……
