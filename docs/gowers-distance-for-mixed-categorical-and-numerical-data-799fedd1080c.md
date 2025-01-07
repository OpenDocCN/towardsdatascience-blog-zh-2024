# 高尔的距离：用于混合分类和数值数据

> 原文：[`towardsdatascience.com/gowers-distance-for-mixed-categorical-and-numerical-data-799fedd1080c?source=collection_archive---------9-----------------------#2024-07-12`](https://towardsdatascience.com/gowers-distance-for-mixed-categorical-and-numerical-data-799fedd1080c?source=collection_archive---------9-----------------------#2024-07-12)

## 用于聚类混合数据的距离度量

[](https://medium.com/@pelletierhaden?source=post_page---byline--799fedd1080c--------------------------------)![Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--799fedd1080c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--799fedd1080c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--799fedd1080c--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--799fedd1080c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--799fedd1080c--------------------------------) ·阅读时间 8 分钟·2024 年 7 月 12 日

--

# 什么是距离度量？

你很可能听说过曼哈顿距离或欧几里得距离。这是两种不同的度量方式，用于提供两个给定数据点的距离（或差异）信息。

![](img/8c352a266faf7e7cf49c742022e2737c.png)

曼哈顿距离和欧几里得距离的图示。图片由作者提供

简而言之，**欧几里得距离**是从点 A 到点 B 的最短距离。**曼哈顿距离**计算 x 和 y 坐标之间的绝对差的总和，并根据它们在网格上的位置来计算距离，假设你只能上下左右移动（不能斜着走）。

距离度量通常是**聚类算法**的基础，例如使用欧几里得距离的*k-means 聚类*。这很有道理，因为为了定义聚类，首先需要知道两个数据点有多相似或有多不同（即它们之间的距离有多远）。

## 计算两点之间的距离

为了展示这个过程，我将以使用欧几里得距离为例。
