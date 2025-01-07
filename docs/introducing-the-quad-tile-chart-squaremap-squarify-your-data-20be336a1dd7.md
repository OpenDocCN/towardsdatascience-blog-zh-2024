# 介绍四格图与方形图：将数据方形化

> 原文：[`towardsdatascience.com/introducing-the-quad-tile-chart-squaremap-squarify-your-data-20be336a1dd7?source=collection_archive---------4-----------------------#2024-02-03`](https://towardsdatascience.com/introducing-the-quad-tile-chart-squaremap-squarify-your-data-20be336a1dd7?source=collection_archive---------4-----------------------#2024-02-03)

## 了解四格图并使用 Python 创建自己的图表

[](https://medium.com/@nickgerend?source=post_page---byline--20be336a1dd7--------------------------------)![Nick Gerend](https://medium.com/@nickgerend?source=post_page---byline--20be336a1dd7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--20be336a1dd7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--20be336a1dd7--------------------------------) [Nick Gerend](https://medium.com/@nickgerend?source=post_page---byline--20be336a1dd7--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--20be336a1dd7--------------------------------) ·阅读时间 34 分钟 ·2024 年 2 月 3 日

--

![](img/dfc443ba2c74ef8fbc3c957e2627e42f.png)

四格图和方形图由 Nick Gerend 创作

# 介绍

在数据可视化中，可用画布的大小和形状始终是一个关键因素。这是我在另一篇最近的文章中提到的话题，我在文章中介绍了一种名为 [水晶条形图](https://medium.com/towards-data-science/introducing-the-crystal-bar-chart-visualizing-sequential-differential-clustering-21faa4a3fed1) 的图表，采用两轴方法来节省空间。

[](/introducing-the-crystal-bar-chart-visualizing-sequential-differential-clustering-21faa4a3fed1?source=post_page-----20be336a1dd7--------------------------------) ## 介绍水晶条形图：可视化顺序差异聚类

### 了解水晶条形图并使用 Python 创建自己的图表

towardsdatascience.com

在本文中，我将探讨我开发的一种无轴方法，将一组值可视化为方形，并通过将容器填充为凸多边形或简单凹多边形的形式来节省空间。我还尽力将代码填充到本文中，希望它能够作为一个有趣的教程，帮助你了解其他一些可能有用的技巧和可视化方法。

本文的目标是考虑标准和非传统的容器形状（如下所示），并以优化空间、提供直观大小的方式，将一组值呈现为这些形状中的相似形状。
