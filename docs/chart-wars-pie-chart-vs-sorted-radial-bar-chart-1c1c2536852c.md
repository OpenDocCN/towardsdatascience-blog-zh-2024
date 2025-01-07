# 图表大战：饼图与排序径向条形图

> 原文：[`towardsdatascience.com/chart-wars-pie-chart-vs-sorted-radial-bar-chart-1c1c2536852c?source=collection_archive---------5-----------------------#2024-07-01`](https://towardsdatascience.com/chart-wars-pie-chart-vs-sorted-radial-bar-chart-1c1c2536852c?source=collection_archive---------5-----------------------#2024-07-01)

## 快速成功数据科学

## 既能享受饼图，又能得到所有信息！

[](https://medium.com/@lee_vaughan?source=post_page---byline--1c1c2536852c--------------------------------)![Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--1c1c2536852c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1c1c2536852c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1c1c2536852c--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--1c1c2536852c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1c1c2536852c--------------------------------) ·8 分钟阅读·2024 年 7 月 1 日

--

![](img/60b1fa5b6a1d9911ad2d3c2134248f87.png)

一个排序的径向条形图（作者制作）

条形图可能是图表之王，但让我们面对现实，它们很无聊。一些流行的替代方案，比如饼图，也没好到哪里去。这里是一个饼图，展示了德国 2023 年电力的来源：

![](img/7c7171be25b7bf3ca2d399f475965343.png)

2023 年德国电力来源的饼图（作者来自[维基百科](https://en.wikipedia.org/wiki/Energy_in_Germany)）

这也很无聊。平面且静态。更糟的是，人类在通过*区域*进行区分时并不擅长，这使得这个图表难以解读。

现在，让我们尝试一种叫做*排序径向条形图*的小工具：

![](img/a6d701f3c6b8b7f8df2154bce7749333.png)

2023 年德国电力来源的径向条形图（作者来自[维基百科](https://en.wikipedia.org/wiki/Energy_in_Germany)）

这个图表引人注目且充满动感。最大和最小的扇区都会吸引眼球。你*想要*探索这个图表！

Python 的流行库 Matplotlib 通过在*极坐标轴*上绘制条形图来生成径向条形图。在这个*快速成功数据科学*项目中，我们将使用 Python、NumPy、pandas 和 Matplotlib 来生成一个排序的德国能源来源径向条形图。
