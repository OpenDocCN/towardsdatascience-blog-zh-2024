# 未来可持续的机器学习项目的 7 个建议

> 原文：[https://towardsdatascience.com/7-tips-to-future-proof-machine-learning-projects-582397875edc?source=collection_archive---------13-----------------------#2024-02-24](https://towardsdatascience.com/7-tips-to-future-proof-machine-learning-projects-582397875edc?source=collection_archive---------13-----------------------#2024-02-24)

## 开发更具协作性、可复现性和可重用性的机器学习代码简介

[](https://destingong.medium.com/?source=post_page---byline--582397875edc--------------------------------)[![Destin Gong](../Images/c93d4341a928c36bc47031f02e23ebbf.png)](https://destingong.medium.com/?source=post_page---byline--582397875edc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--582397875edc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--582397875edc--------------------------------) [Destin Gong](https://destingong.medium.com/?source=post_page---byline--582397875edc--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--582397875edc--------------------------------) ·8 分钟阅读·2024年2月24日

--

![](../Images/4fc99203f129fd425c8893e1ccff4ff1.png)

7 个建议确保机器学习项目的未来可持续性（作者图片）

在从探索性机器学习项目（通常用于研究和学习）过渡到行业级项目时，可能会出现知识差距。这是因为行业项目通常有三个额外的目标：协作性、可复现性和可重用性，这些目标旨在增强业务连续性、提高效率并降低成本。虽然我离找到完美的解决方案还远，但我希望记录一些建议，将探索性、基于笔记本的机器学习代码转变为一个更具可扩展性和可持续性的行业级项目。

我将这些建议分为三个关键策略：

+   改进 1：模块化 — 将代码拆分成更小的部分

+   改进 2：版本管理 — 数据、代码和模型版本管理

+   改进 3：一致性 — 一致的结构和命名约定

# 改进 1：模块化 — 将代码拆分成更小的部分

## **问题陈述**

> 我面临的一个挑战是只有一个笔记本用于...
