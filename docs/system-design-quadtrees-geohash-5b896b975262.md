# 系统设计：四叉树与 GeoHash

> 原文：[`towardsdatascience.com/system-design-quadtrees-geohash-5b896b975262?source=collection_archive---------1-----------------------#2024-05-08`](https://towardsdatascience.com/system-design-quadtrees-geohash-5b896b975262?source=collection_archive---------1-----------------------#2024-05-08)

## 现实世界应用中优化搜索的高效地理数据管理

[](https://medium.com/@slavahead?source=post_page---byline--5b896b975262--------------------------------)![Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--5b896b975262--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5b896b975262--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5b896b975262--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--5b896b975262--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5b896b975262--------------------------------) ·阅读时间：6 分钟·2024 年 5 月 8 日

--

![](img/4953d153e80e31607f6d049670119d1b.png)

# 引言

Google Maps 和 Uber 只是使用地理数据的最流行应用程序中的一部分示例。存储全球数百万个地点的信息要求它们高效地存储和处理地理位置，包括距离计算和最近邻搜索。

所有现代地理应用程序都使用以经度和纬度表示的二维位置。虽然将地理数据存储为坐标对可能显得很天真，但这种方法中存在一些陷阱。

在本文中，我们将讨论天真方法背后的潜在问题，并介绍另一种用于加速大规模系统中数据处理的现代格式。

> 注意：在本文中，我们将把世界表示为一个大的平面二维矩形，而不是三维椭球体。经度和纬度将分别用 X 和 Y 坐标表示。这种简化有助于使解释过程更为简便，同时不会遗漏主要细节。

# 问题

假设有一个数据库存储所有应用对象的二维坐标。用户登录后……
