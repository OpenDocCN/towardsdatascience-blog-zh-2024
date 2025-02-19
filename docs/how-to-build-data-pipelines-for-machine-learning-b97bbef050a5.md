# 如何为机器学习构建数据管道

> 原文：[`towardsdatascience.com/how-to-build-data-pipelines-for-machine-learning-b97bbef050a5?source=collection_archive---------1-----------------------#2024-05-02`](https://towardsdatascience.com/how-to-build-data-pipelines-for-machine-learning-b97bbef050a5?source=collection_archive---------1-----------------------#2024-05-02)

## 面向初学者的介绍，附带 Python 代码

[](https://shawhin.medium.com/?source=post_page---byline--b97bbef050a5--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--b97bbef050a5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b97bbef050a5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b97bbef050a5--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--b97bbef050a5--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b97bbef050a5--------------------------------) ·阅读时间：10 分钟·2024 年 5 月 2 日

--

这是关于全栈数据科学（FSDS）[系列文章](https://shawhin.medium.com/list/full-stack-data-science-f0910c75d006)的第三篇。在上一篇文章中，我介绍了一个用于构建机器学习（ML）解决方案的五步项目管理框架。虽然机器学习可能让人联想到复杂的算法和技术，但**ML 解决方案的质量取决于** **可用数据的质量**。这就提出了在 FSDS 中对数据工程（DE）技能的需求。本文将讨论在此背景下最关键的 DE 技能，并通过一个真实的例子进行讲解。

![](img/ce292096dc0d9448a048924c5ce6e0b8.png)

图片由[the blowup](https://unsplash.com/@theblowup?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

全栈数据科学（FSDS）涉及端到端管理和实施机器学习解决方案。**数据工程**对于这个过程至关重要，即**使数据能够轻松地用于分析和机器学习应用**[1]。

虽然这可能涉及广泛的任务（例如数据建模、设计架构、管理分布式系统），但在 FSDS 的背景下，数据工程归结为一件关键的事情——构建数据管道。

**数据管道** **将数据从 A 点传输到 B 点**。例如，抓取网页、重格式化数据并将其加载到数据库中。数据管道包括三个步骤——提取（E）、转换（T）和加载（L），可以通过两种方式组合——ETL 或 ELT。
