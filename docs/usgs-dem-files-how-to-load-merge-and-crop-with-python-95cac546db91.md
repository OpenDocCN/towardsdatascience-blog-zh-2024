# USGS DEM 文件：如何使用 Python 加载、合并和裁剪

> 原文：[https://towardsdatascience.com/usgs-dem-files-how-to-load-merge-and-crop-with-python-95cac546db91?source=collection_archive---------10-----------------------#2024-12-17](https://towardsdatascience.com/usgs-dem-files-how-to-load-merge-and-crop-with-python-95cac546db91?source=collection_archive---------10-----------------------#2024-12-17)

## 快速成功数据科学

## 数字高程数据准备的快速指南

[](https://medium.com/@lee_vaughan?source=post_page---byline--95cac546db91--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--95cac546db91--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--95cac546db91--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--95cac546db91--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--95cac546db91--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--95cac546db91--------------------------------) ·阅读时间 11 分钟·2024年12月17日

--

![](../Images/c2bf9d00b208ed8f068ab31c24a5896d.png)

美国地质调查局数字高程模型（作者提供）

*数字高程模型（DEM）* 是地球表面的 3D 数字表示。它记录了不同点的 *海拔高度*，这些点可以通过传统的测量技术、激光雷达（LIDAR）、卫星影像、航拍或 GPS 测量来获得。DEM 通常以栅格格式存储，每个像素都有一个高程值。

DEM 是在需要使用物理景观进行规划和决策的领域中至关重要的工具。其应用包括地形绘图、水文建模、城市规划、环境研究，以及为虚拟现实、游戏和模拟创建逼真的地形模型。

美国地质调查局（USGS）提供了可从[国家地图下载器](https://apps.nationalmap.gov/downloader/)下载的[免费 DEM](https://www.usgs.gov/tools/national-map-viewer)。这些是无缝栅格文件，称为 *DEM TIFFs* 或 *GeoTIFFs*。虽然与标准的照片 TIFF 类似，但这些文件包含嵌入的地理空间元数据。

在这个 *快速成功数据科学* 项目中，我们将为选定的地理区域准备 DEM TIFF 文件。这不会像从网站下载文件那样简单。现有的 USGS 文件很少与我们所选的区域完全匹配...
