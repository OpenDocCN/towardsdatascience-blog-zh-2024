# 在 Python 中栅格化矢量数据 — 乐高地图

> 原文：[`towardsdatascience.com/rasterizing-vector-data-in-python-84d97f4b3fa6?source=collection_archive---------7-----------------------#2024-11-05`](https://towardsdatascience.com/rasterizing-vector-data-in-python-84d97f4b3fa6?source=collection_archive---------7-----------------------#2024-11-05)

![](img/b570468c9df0492f48e504cf12cc5071.png)

## 如何将矢量高程线转化为网格 — 并用乐高积木构建

[](https://medium.com/@janosovm?source=post_page---byline--84d97f4b3fa6--------------------------------)![Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--84d97f4b3fa6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--84d97f4b3fa6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--84d97f4b3fa6--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--84d97f4b3fa6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--84d97f4b3fa6--------------------------------) ·阅读时间：5 分钟·2024 年 11 月 5 日

--

矢量数据和栅格数据是两种主要的空间数据结构。矢量数据非常适合存储精确的位置和形状，如点、线和多边形。相比之下，栅格数据通过像素网格来表示空间特征，每个像素存储特定的值。不同的数据源和应用程序产生不同的数据结构；然而，在进行高级空间分析时，我们通常需要将这两种不同类型的数据结合起来。在本文中，我将给出一个例子 —— 如何将矢量数据（在本例中是高程线）转化为栅格网格单元。此外，我还展示了如何通过将每个栅格网格单元与一个小乐高积木匹配来进行可视化。

*所有图片均由作者创建。*

# 数据

作为数据来源，我使用了[布达佩斯开放数据地图集](https://atlo.team/boda/)提供的开放数据，其中包含该城市的高程图层。在下载了空间数据文件后，让我们使用 GeoPandas 来查看它：

```py
import geopandas as gpd

gdf = gpd.read_file('bpelev.json')

print(len(gdf))
gdf.head(5)
```

本单元的输出：
