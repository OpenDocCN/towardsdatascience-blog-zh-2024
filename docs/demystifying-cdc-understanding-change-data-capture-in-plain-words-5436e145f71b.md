# 破解CDC：用简单的语言理解变更数据捕获

> 原文：[https://towardsdatascience.com/demystifying-cdc-understanding-change-data-capture-in-plain-words-5436e145f71b?source=collection_archive---------8-----------------------#2024-03-18](https://towardsdatascience.com/demystifying-cdc-understanding-change-data-capture-in-plain-words-5436e145f71b?source=collection_archive---------8-----------------------#2024-03-18)

## 你必备的变更数据捕获指南

[](https://medium.com/@antoniograndinetti91?source=post_page---byline--5436e145f71b--------------------------------)[![Antonio Grandinetti](../Images/f97ba21a6eb1bf1f66174953511afcd2.png)](https://medium.com/@antoniograndinetti91?source=post_page---byline--5436e145f71b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5436e145f71b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5436e145f71b--------------------------------) [Antonio Grandinetti](https://medium.com/@antoniograndinetti91?source=post_page---byline--5436e145f71b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5436e145f71b--------------------------------) ·阅读时间：4分钟·2024年3月18日

--

在我的工作经验中（大数据分析和数据工程领域），项目通常各不相同，但它们总是遵循一个已确立的架构：目标是创建一个**数据平台**，从不同的数据源收集数据，执行一系列处理，然后将汇总后的数据提供给那些需要使用它的人。

![](../Images/44bfda3016082f6036af858cc968c41a.png)

图片来自[ian dooley](https://unsplash.com/@sadswim?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

上述描述的架构通常总结为数据湖（Data Lake）/数据湖屋（Data Lakehouse）和ETL（提取-转换-加载）流程。提取数据的不同方式可以分为两类：

+   **批处理**：整个数据集在一次操作中从源系统提取

+   **流式处理**：数据提取是持续进行的，实时监控源系统的任何变化。数据一旦发生修改就会被提取。

每年都会出现新技术、新架构和新方法，但有一种方法持续被广泛使用，那就是**变更数据捕获**。

**什么是变更数据捕获（CDC）？🤓**

变更数据捕获是一种设计模式，使你能够捕获数据源中发生的变化。它提供了一个持续的数据更新流，可以……
