# 线性规划简介 — 第一部分

> 原文：[`towardsdatascience.com/optimize-production-with-r-part-i-25497d29e06e?source=collection_archive---------15-----------------------#2024-06-11`](https://towardsdatascience.com/optimize-production-with-r-part-i-25497d29e06e?source=collection_archive---------15-----------------------#2024-06-11)

## 使用 R 优化生产

[](https://medium.com/@rlohne?source=post_page---byline--25497d29e06e--------------------------------)![Robert Lohne](https://medium.com/@rlohne?source=post_page---byline--25497d29e06e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--25497d29e06e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--25497d29e06e--------------------------------) [Robert Lohne](https://medium.com/@rlohne?source=post_page---byline--25497d29e06e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--25497d29e06e--------------------------------) ·阅读时间 8 分钟·2024 年 6 月 11 日

--

去年，我的一位朋友找到了我，他在一家小型的家族拥有的钢铁和金属公司工作。他想知道是否能创建一个工具，帮助他解决在切割钢梁时如何最小化浪费的问题。这听起来像是一个适合用线性规划解决的问题！

当我刚开始时，关于如何在 R 中使用线性规划的初学者文章并不多，而且这些文章对于数学基础不深的人来说很难理解。到 2023 年初，ChatGPT 在 R 中使用线性规划的方面表现并不出色，因此我曾经希望能有一份这样的指南。

本系列是我尝试编写的一份指南。希望它能对某些人有所帮助。

本系列的**第一部分**将介绍 R 语言中的线性规划概念，并通过一个基本的例子进行讲解。在[**第二部分**](https://medium.com/p/4fa9521ac3a7#8ceb-e636a4247639)中，我将教你如何创建一个更高级的模型。如果需求足够，我可能会扩展到**第三部分**，在其中我会详细介绍如何创建一个 Shiny 应用，实际使用线性规划来优化工作。

# 线性规划

线性规划涉及到找到线性函数的最优解。常见的例子有背包问题（给定一组物品，每个物品有一个价值和重量，要求找出哪些物品应放入背包，以使得不超过重量限制的同时，最大化所选物品的总价值）和旅行商问题（给定一组...
