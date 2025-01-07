# 步骤指南：如何在 Plotly 中构建 Waffle 图表

> 原文：[https://towardsdatascience.com/step-by-step-guide-for-building-waffle-charts-in-plotly-1f7d26f2a215?source=collection_archive---------5-----------------------#2024-11-25](https://towardsdatascience.com/step-by-step-guide-for-building-waffle-charts-in-plotly-1f7d26f2a215?source=collection_archive---------5-----------------------#2024-11-25)

## 学习如何使用 Plotly 在 Python 中创建自定义 Waffle 图表进行数据可视化

[](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--1f7d26f2a215--------------------------------)[![Amanda Iglesias Moreno](../Images/9cff03d0c0caaa9a8aa53c4c34c90cac.png)](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--1f7d26f2a215--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1f7d26f2a215--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1f7d26f2a215--------------------------------) [Amanda Iglesias Moreno](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--1f7d26f2a215--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1f7d26f2a215--------------------------------) ·11 分钟阅读·2024年11月25日

--

![](../Images/e71973276740c2b0d03ca11271ee5fea.png)

Waffle 图表在 Plotly 中（图像来源：作者）

**Plotly 是一个功能最为全面的 Python 数据可视化库之一**，毫无疑问，也是我最喜欢的库。它已经定义了广泛的可视化方式，从基本的图表（如柱状图或饼图）到更具体的统计学或数据科学领域的可视化（如箱线图或树状图）。

**Plotly 提供的可视化选项非常广泛**；然而，某些可视化在库中并不存在。这并不意味着我们无法创建这些可视化。通过一点巧思，结合 Plotly 提供的定制和可视化选项，我们可以创造出许多在表面上看似不可能实现的可视化，其中之一就是 Waffle 图表。

**本文将解释如何使用 Plotly 创建 Waffle 图表**。从热图开始，通过一点想象力和创造力，我们将看到创建这种类型的可视化其实比看起来更简单。

# 1. 为什么选择 Waffle 图表？

**Waffle 图表是饼图或柱状图的一个有趣替代方案，当你想用另一种布局展示比例时，它非常合适**…
