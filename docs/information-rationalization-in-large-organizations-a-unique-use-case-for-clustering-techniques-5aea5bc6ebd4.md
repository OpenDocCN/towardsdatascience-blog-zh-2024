# 大型组织中的数据调和难题

> 原文：[`towardsdatascience.com/information-rationalization-in-large-organizations-a-unique-use-case-for-clustering-techniques-5aea5bc6ebd4?source=collection_archive---------11-----------------------#2024-04-18`](https://towardsdatascience.com/information-rationalization-in-large-organizations-a-unique-use-case-for-clustering-techniques-5aea5bc6ebd4?source=collection_archive---------11-----------------------#2024-04-18)

## 我们如何利用聚类技术来整合和重构大量不相干的仪表板？

[](https://medium.com/@rkumar5680?source=post_page---byline--5aea5bc6ebd4--------------------------------)![Ramkumar K](https://medium.com/@rkumar5680?source=post_page---byline--5aea5bc6ebd4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5aea5bc6ebd4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5aea5bc6ebd4--------------------------------) [Ramkumar K](https://medium.com/@rkumar5680?source=post_page---byline--5aea5bc6ebd4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5aea5bc6ebd4--------------------------------) ·阅读时间 11 分钟·2024 年 4 月 18 日

--

![](img/bcae24d24bf41e4ff935fb3b1e30a2ff.png)

图片由[Luke Chesser](https://unsplash.com/@lukechesser?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 背景

组织每天都会生成大量数据。仪表板被用来分析这些数据并推导出有意义的商业洞察，以及跟踪关键绩效指标（KPI）。随着时间的推移，我们会积累数百个（或更多）这样的仪表板。通常，这种*仪表板激增*是由多个小组在没有对已有分析结果有可视化了解的情况下，独立开发各自的分析工具所推动的。例如，销售团队可能会创建一个仪表板来跟踪其预测与实际销售情况，而不知道供应链团队已经有一个用于跟踪*预测准确性*的仪表板。这样不仅导致了重复劳动，而且我们可能会在多个仪表板中看到相同的指标和分析结果，却没有一个统一的数据源。

当团队汇报业务状态给高层管理时，他们通常需要花费数小时来调和不同的指标，以便向领导层提供清晰和连贯的信息。假设我们有一个 1000 人的组织，其中每个人每周平均需要花费 2 小时来进行此类工作……
