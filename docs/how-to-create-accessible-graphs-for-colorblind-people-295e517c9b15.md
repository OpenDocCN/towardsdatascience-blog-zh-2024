# 如何为色盲人士创建可访问的图表

> 原文：[`towardsdatascience.com/how-to-create-accessible-graphs-for-colorblind-people-295e517c9b15?source=collection_archive---------12-----------------------#2024-02-05`](https://towardsdatascience.com/how-to-create-accessible-graphs-for-colorblind-people-295e517c9b15?source=collection_archive---------12-----------------------#2024-02-05)

## 包括一个 Python 色盲模拟器和合适的配色方案

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--295e517c9b15--------------------------------)![Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--295e517c9b15--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--295e517c9b15--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--295e517c9b15--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--295e517c9b15--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--295e517c9b15--------------------------------) ·阅读时间 7 分钟·2024 年 2 月 5 日

--

![](img/c6d907e86a8d57309ec1930909f1c7d7.png)

该图片由作者使用 Midjourney 创建。

色盲，或更准确地说，色觉缺陷，影响了多达 8%的男性和 0.5%的女性。最常见的类型是[红绿色盲](https://en.wikipedia.org/wiki/Color_blindness)，这是一种先天性疾病，患者的视网膜缺少红色或绿色的感光细胞。因此，他们无法区分如红与绿、青与灰、蓝与紫等颜色对。

对数据科学家来说，在选择图表的配色方案时考虑色盲用户非常重要。相当一部分用户会有某种形式的色觉障碍，他们可能会以不同于预期的方式理解图表。实际上，当我的直属上司无法阅读我的折线图时，我意识到了这一点，后来才发现他在区分红色和绿色时有困难。

在这篇文章中，我将分享

+   一个用于模拟最常见色盲类型的 Python 工具

+   对 matplotlib 和 seaborn 默认配色方案的可访问性测试

+   使用 ColorBrewer 创建可访问的配色方案

## 色盲是什么样子的？
