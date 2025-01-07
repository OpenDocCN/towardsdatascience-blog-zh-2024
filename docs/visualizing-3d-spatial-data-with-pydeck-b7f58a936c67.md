# 使用 PyDeck 可视化 3D 空间数据

> 原文：[`towardsdatascience.com/visualizing-3d-spatial-data-with-pydeck-b7f58a936c67?source=collection_archive---------6-----------------------#2024-07-29`](https://towardsdatascience.com/visualizing-3d-spatial-data-with-pydeck-b7f58a936c67?source=collection_archive---------6-----------------------#2024-07-29)

![](img/f969594cc418e583a6c70573cb6b9bff.png)

## 如何在 Python 中创建建筑模型地图

[](https://medium.com/@janosovm?source=post_page---byline--b7f58a936c67--------------------------------)![Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--b7f58a936c67--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b7f58a936c67--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b7f58a936c67--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--b7f58a936c67--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b7f58a936c67--------------------------------) ·阅读时间 5 分钟·2024 年 7 月 29 日

--

在这篇文章中，我想向大家介绍一个名为[PyDeck](https://deckgl.readthedocs.io/en/latest/)的 Python 包，这是一个用于在 Python 中创建 3D 地图的好工具。作为示例，我将使用一个覆盖布达佩斯市的 3D 建筑模型数据库。

*所有图像均由作者创建。*

# 访问建筑数据

数据来自公开的[布达佩斯开放数据地图](https://atlo.team/boda/)，你可以在这里访问该数据。虽然该网站是匈牙利语的，但一旦你找到‘Épületmagasság’部分，并点击以‘geojson’开头的按钮，你应该能够下载 geojson 文件，文件内容完全可以用英语和 Python 读取。

![](img/e666aeaaa4b89d562c490860e9176559.png)

来源

在快速下载后，让我们打开文件并快速查看：

```py
# import geopandas 
import geopandas as gpd

# parse the building height data set
gdf = gpd.read_file('epuletmagassag.geojson')
gdf.plot()
len(gdf)
```

这个单元的输出：
