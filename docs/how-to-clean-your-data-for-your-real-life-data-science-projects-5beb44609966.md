# 如何清洗你的数据以应对现实生活中的数据科学项目

> 原文：[`towardsdatascience.com/how-to-clean-your-data-for-your-real-life-data-science-projects-5beb44609966?source=collection_archive---------3-----------------------#2024-12-23`](https://towardsdatascience.com/how-to-clean-your-data-for-your-real-life-data-science-projects-5beb44609966?source=collection_archive---------3-----------------------#2024-12-23)

## 简化数据科学

## 我如何处理缺失值——快速的 Python 指南

[](https://medium.com/@krishnan.mythili?source=post_page---byline--5beb44609966--------------------------------)![Mythili Krishnan](https://medium.com/@krishnan.mythili?source=post_page---byline--5beb44609966--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5beb44609966--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5beb44609966--------------------------------) [Mythili Krishnan](https://medium.com/@krishnan.mythili?source=post_page---byline--5beb44609966--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5beb44609966--------------------------------) ·阅读时间 7 分钟·2024 年 12 月 23 日

--

![](img/79e1b92b496905ee9445adb8dcce7d4f.png)

图片来自 [Wannapik](https://www.wannapik.com/vectors/67398?search%5Bquery%5D=missing)

我们常常听到——“哦，现在有很多可以做所有事情的软件包！使用这些软件包跑模型只需 10 分钟。”是的，确实有这些软件包——但它们只有在你已经准备好干净的数据集时才能工作。那么，从多个来源创建、整理并清洗一个适合用途的数据集需要多长时间呢？问问那些正在努力创建数据集的数据科学家吧。所有那些曾经花费数小时清洗数据、研究、阅读并重写代码、失败然后再重写的人，都会同意我的看法！这就引出了一个问题：

> ‘现实生活中的数据科学是 70%的数据清洗和 30%的实际建模或分析’

因此，我想，不如回归基础，学习如何清洗数据集，并使其能够更高效地解决业务问题。我们将以缺失值处理作为本系列的起点。以下是议程：

1.  **什么是缺失值**

1.  **数据集中的缺失值的原因是什么**

1.  **缺失值为什么很重要**

1.  **处理缺失值的方法**
