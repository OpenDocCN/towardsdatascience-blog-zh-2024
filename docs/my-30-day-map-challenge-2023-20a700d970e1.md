# 我的30天地图挑战2023

> 原文：[https://towardsdatascience.com/my-30-day-map-challenge-2023-20a700d970e1?source=collection_archive---------5-----------------------#2024-06-06](https://towardsdatascience.com/my-30-day-map-challenge-2023-20a700d970e1?source=collection_archive---------5-----------------------#2024-06-06)

## 选定地图主题和算法概述

[](https://bryanvallejo16.medium.com/?source=post_page---byline--20a700d970e1--------------------------------)[![Bryan R. Vallejo](../Images/fd92974f57c72875cc133a2c959d64ca.png)](https://bryanvallejo16.medium.com/?source=post_page---byline--20a700d970e1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--20a700d970e1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--20a700d970e1--------------------------------) [Bryan R. Vallejo](https://bryanvallejo16.medium.com/?source=post_page---byline--20a700d970e1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--20a700d970e1--------------------------------) ·9分钟阅读·2024年6月6日

--

![](../Images/67f08588cd8f947487c5687955b3c485.png)

作者提供的图片。精选自我创作的地图拼贴。

# 介绍

正如你们中的许多人所知道的，30天地图挑战是一个社交媒体活动，热爱地图的人们在每年的11月为期30天内围绕不同的主题进行地图设计。你可以在 [官方网站](https://30daymapchallenge.com/) 上找到更多信息。很少有人能够处理每个主题都能完全呈现的地图，并且每天在社交媒体上发布。另一方面，有些人会选择自己最喜欢的主题，只发布这些天的地图。我属于第二组人。

在这个故事中，我想描述我所经历的地图设计过程的概述。我选择了我最喜欢的主题，并使用算法为它们增色。我相信，创建能够展示“某种其他内容”的地图非常重要，而最好的方式就是通过算法的结果。地图应在第一眼给人一个清晰的视觉信息，如果可能的话，还能为经验丰富的地图阅读者提供更多的详细信息。

让我们来看看这些地图。

# 数据

+   **开放街图数据。** 根据 [开放数据共享开放数据库许可证（ODbl）](https://opendatacommons.org/licenses/odbl/) 或署名许可协议授权。用户可以自由复制、分发、传输和改编数据，只要注明作者，如 © [OpenStreetMap](https://www.openstreetmap.org/copyright) 贡献者。

+   **Kontur人口数据集**。Kontur人口数据集依据 [创意共享署名国际（CC BY）许可协议](https://data.humdata.org/faqs/licenses) 提供。你可以用于任何目的，包括商业用途。

+   **OpenCell ID**。OpenCell ID的数据库是根据[创作共用署名-相同方式共享 4.0 国际许可证](https://creativecommons.org/licenses/by-sa/4.0/)授权的。

+   **MODIS NASA DAAC。** 该集合符合NASA的[数据和信息政策](https://modaps.modaps.eosdis.nasa.gov/services/faq/LAADS_Data-Use_Citation_Policies.pdf)，该政策促进研究与应用社区、私营行业、学术界和公众之间的完全开放数据共享（数据包括观测数据、图像、元数据、产品和文档）。

+   **塔尔图开放数据。** 根据[创作共用署名-相同方式共享 3.0 未移植版本 (CC-BY-SA 3.0)](https://creativecommons.org/licenses/by-sa/3.0/)的许可，我们可以自由地分享、复制、再分发、改编、混合或基于其进行创作，即使是用于商业目的。

+   **SEO BirdLife数据。** 数据集具有私人许可证，仅供GIS4 Wildlife及其代表使用。主要是，视觉化产品可以公开分享，但不包括代码、算法、数据或数据衍生物。

+   **IUCN红色名录。** [数据](https://www.iucnredlist.org/resources/spatial-data-download)可供**非商业使用**，以帮助为保护规划和其他非商业决策过程提供信息（请参阅[使用条款和条件](https://www.iucnredlist.org/terms/terms-of-use)）。

# 点

临近点地图表示通过每个OpenStreetMap（OSM）建筑物与OSM消防站之间的距离来衡量的接近度。每个点是每个建筑物的质心。然后，我使用[**最近邻**](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.BallTree.html) **算法**归属了欧几里得距离。这个算法节省了大量的计算时间。通过将接近度（距离）作为点的大小（最小且红色表示较远的建筑）为点图添加了不错的视觉效果。查看这个教程。

[](/nearest-neighbor-analysis-for-large-datasets-in-helsinki-79ebc7288c91?source=post_page-----20a700d970e1--------------------------------) [## 赫尔辛基地区大数据集的最近邻分析

### BallTree函数用于高效的距离分析：公交车站和餐馆

towardsdatascience.com](/nearest-neighbor-analysis-for-large-datasets-in-helsinki-79ebc7288c91?source=post_page-----20a700d970e1--------------------------------)

该地图是使用QGIS制作的。

![](../Images/05e6cda465968a946e56fb64e0e4a818.png)

作者提供的图像。点。

# 线条

该线路地图展示了OSM住宅楼与中央火车站之间的累计旅行距离。由于我有超过19,000座住宅楼，我需要构建正好19070条路线。幸运的是，我在芬兰阿尔托大学工作时有一台16核超级计算机，可以并行处理这个过程。路径是通过OSMnx Python库中的**最短路径（Dijkstra算法）**生成的。地图中有一个很好的视觉效果，最短路径到市中心的线路宽度较细（白色），而较长的路径（红色）则宽一些。

这张地图是在QGIS中制作的。

![](../Images/da0463369b0f0f34f8665b65960c9225.png)

图片来自作者。线路

**多边形**

该多边形地图是通过从赫尔辛基中央火车站生成的**等时线**制作的。生成2分钟等时线的过程非常具有挑战性，因为Valhalla API经常崩溃。但经过一些测试，终于成功了。我使用了**routingpy** Python库来完成这个任务。

这张地图的视觉效果是移除海洋上的多边形。幸运的是，我成功地将由陆地限定的等时线与准确的海洋图层进行了裁剪。我不建议过于挑剔，因为这个裁剪过程花费了我很多时间。

我修改了这张地图，来自于[原始帖子](https://x.com/BryanRVallejo/status/1720342295281803428)，因为最好保持等时线的自然边界。

这张地图是在QGIS中制作的。

![](../Images/040967501671451cbfa2a734ee8a3078.png)

图片来自作者。多边形

# 导航

导航地图显示了摩洛哥两点之间的两条路线。起点和终点是由一名需要帮助的学生提供的。我想了解如果一条路径按距离加权，另一条按时间加权，它们会有何不同。因此，最短路径和最快路径。路径是通过使用OSMnx Python库的**最短路径（Dijkstra算法）**生成的。

我在地图背后添加了道路网络，并给它做了3D旋转。代码可以在线获取。

[](/dijkstras-algorithm-weighted-by-travel-time-in-osm-networks-792aa92e03af?source=post_page-----20a700d970e1--------------------------------) [## Dijkstra算法加权旅行时间在OSM网络中的应用

### 使用OSMNX 1.6找到最快和最短的路径

[towardsdatascience.com](/dijkstras-algorithm-weighted-by-travel-time-in-osm-networks-792aa92e03af?source=post_page-----20a700d970e1--------------------------------)

这张地图是在KeplerGl中制作的。

![](../Images/d7686888010c6f0c29de9d3e246018a0.png)

图片来自作者。导航。

# 六边形

这张六边形地图是使用[Kontur人口数据](https://data.humdata.org/dataset/kontur-population-spain)制作的。该数据集按国家在400米的分辨率下聚合的人口，清晰地展示了人口密度。这个地图的挑战在于如何将加那利群岛加到地图的一侧。幸运的是，QGIS可以添加不同位置的框架，因此我包括了所有的岛屿。

如果你愿意像这个例子一样在六边形中进行聚合，可以使用[H3-pandas Python库](https://h3-pandas.readthedocs.io/en/latest/)。

使用QGIS制作的地图。

![](../Images/ca68c767924a095d1eb0ca64ce35a61f.png)

作者提供的图像。六边形

# **北美**

这张地图是通过使用[H3-pandas Python库](https://h3-pandas.readthedocs.io/en/latest/)在全球范围内制作的。数据聚合在H3分辨率6中，使用的是包含全球蜂窝天线的OpenCellID数据。然后，我框定了北美地区。这张地图的挑战在于读取全球数据。幸运的是，这一过程可以通过使用[Dask Python库](https://docs.dask.org/en/stable/)完成。

你可以在网上找到处理蜂窝天线数据所需的代码。

[](/mobile-network-operators-coverage-in-ecuador-9fdd78d3b4fb?source=post_page-----20a700d970e1--------------------------------) [## 厄瓜多尔的移动网络运营商覆盖情况

### 蜂窝天线的2G、3G和4G发展地理可视化

[towardsdatascience.com](/mobile-network-operators-coverage-in-ecuador-9fdd78d3b4fb?source=post_page-----20a700d970e1--------------------------------)

使用QGIS制作的地图。

![](../Images/b34b5a42ce85f90af80cebaa7c456f90.png)

作者提供的图像。北美。

# 南美

每次提到南美时，我都会想到厄瓜多尔海岸线的温暖海水。我想展示海表温度（SST）如何被上色而显得如此吸引人。在这张地图中，我使用了2022年11月的MODIS OB DAAC全球级数据集。然后，我使用QGIS框定了南美地区。这张地图的挑战是找到合适的颜色调色板。

如果你想独立处理SST数据，可以在这里找到代码：

[](/monitoring-sea-surface-temperature-at-the-global-level-with-gee-1d7349c7da6?source=post_page-----20a700d970e1--------------------------------) [## 使用GEE在全球范围内监测海表温度

### 如何使用Python为海洋监测创建Streamlit应用

[towardsdatascience.com](/monitoring-sea-surface-temperature-at-the-global-level-with-gee-1d7349c7da6?source=post_page-----20a700d970e1--------------------------------) ![](../Images/0898e81f1aed8660ba1d701045a9ffcc.png)

作者提供的图像。南美。

# 欧洲

制作这张地图是一个愉快的过程。我获得了2022年5月塔尔图（爱沙尼亚）共享单车数据的GPS位置数据集。然后，我使用Python库Movingpandas将高质量的GPS数据转换为轨迹（LineString），这使得操作变得更加方便。我添加了距离并为轨迹上色，呈现出人们如何使用自行车骑行短途和长途的精彩可视化效果。

塔尔图是2024年欧洲文化之都。一般来说，它被认为是一个15分钟城市，因为你可以骑车或步行到任何地方。游客会对这座城市如何轻松实现流动性感到着迷，而这张地图展示了城市各地共享单车的分布情况。

看一下展示如何使用 Movingpandas 的 Python 代码。

[](/bike-sharing-system-movements-to-the-metallica-concert-in-tartu-estonia-1af8361bc6f?source=post_page-----20a700d970e1--------------------------------) [## 爱沙尼亚塔尔图的 Metallica 音乐会自行车共享系统运动

### 使用 Movingpandas 和 KeplerGl 进行 GPS 跟踪可视化 — 教程

[towardsdatascience.com](/bike-sharing-system-movements-to-the-metallica-concert-in-tartu-estonia-1af8361bc6f?source=post_page-----20a700d970e1--------------------------------)

这张地图是在 QGIS 中制作的。

![](../Images/95eb8001208127c1069946bee604a803.png)

作者提供的图像。欧洲。

# 流动

这张地图是与 [GIS4 Wildlife](https://www.gis4-wildlife.com/) 和 [SEO BirdLife](https://www.gis4-wildlife.com/seobirdlife-migra-geovisualization) 的可视化项目的一部分。这个项目的挑战在于将所有的鸟类迁徙数据（GPS）融入到时空可视化中。这个项目使用了私人代码和算法，但幸运的是，我可以分享最终的可视化结果。事实上，KeplerGl 是进行时空可视化的最佳工具。

[](https://www.gis4-wildlife.com/?source=post_page-----20a700d970e1--------------------------------) [## GIS4 Wildlife 移动分析

### 在“加拉帕戈斯海洋保护区”实施野生动物追踪器为地理空间解决方案提供了帮助……

[www.gis4-wildlife.com](https://www.gis4-wildlife.com/?source=post_page-----20a700d970e1--------------------------------) ![](../Images/851a8bbd3bae765d16ddcb308767d3cc.png)

作者提供的图像。流动。

# 3D

和之前一样，我使用了**Valhalla API**来生成等时线。这里的挑战是为 3D 高程添加合适的数值。我需要加入回退的距离，以便较小的等时线呈现比较大的等时线更高的视图。虽然有点棘手，但对于 3D 地图来说效果不错。

这张地图是使用 KeplerGl 制作的。

![](../Images/0c259cac77a0dbe1e839f4cc266016a0.png)

作者提供的图像。3D

# 南极

当我听到南极时，首先浮现在我脑海中的就是那里的海洋哺乳动物，当然也包括企鹅。幸运的是，我找到了一层显示这些物种分布的图层，并了解到它们被认为是濒危物种。国际自然保护联盟（IUCN）提供了海洋-陆地哺乳动物的分布图层，而这个图层位于南极。这个可视化的挑战在于投影和叠加。幸运的是，QGIS 使用*南极极地立体投影*很好地完成了这项工作。

这张地图是在 QGIS 中制作的。由于数据许可规定，这张地图从此选择中移除。但你可以直接在 Twitter 上查看该地图。

> 👇🦭
> 
> [南极海洋-陆地哺乳动物分布](https://x.com/BryanRVallejo/status/1728315080255631463)

# 点

加拉帕戈斯群岛因其丰富的海洋生物多样性，激发了我对运动生态学和地理空间技术的理解。渔业和鲨鱼种群无疑是全球关注的话题，因此当我听到“点”时，我想到了*全球渔业观察*的渔业数据。这张地图展示了2021年9月至2022年2月期间，*加拉帕戈斯海洋保护区*的渔业强度。这个指数被称为*表观渔业努力*，它为海洋空间规划提供了宝贵的见解。

这张地图的挑战在于点的大小和叠加的效果。这个组合展示了渔业的强度，通过点的大小和亮度来表示。很明显，我们可以看到渔船如何避免穿越加拉帕戈斯海洋保护区的边界，同时保护区内部的渔业活动更为局部，且多为小规模渔业。你可以进一步了解*全球渔业地图*数据以及渔业如何影响这个原始群岛中的海洋大型动物。

[](/unveiling-fishing-activity-risk-to-marine-megafauna-with-geospatial-technologies-48505e44d5e1?source=post_page-----20a700d970e1--------------------------------) [## 利用地理空间技术揭示渔业活动对海洋大型动物的风险

### 受到中国渔船威胁的鲸鲨——加拉帕戈斯群岛

[towardsdatascience.com](/unveiling-fishing-activity-risk-to-marine-megafauna-with-geospatial-technologies-48505e44d5e1?source=post_page-----20a700d970e1--------------------------------)

这张地图是在QGIS中制作的。

![](../Images/b33bf12e3f812e61fe87fda18d9b2268.png)

图片由作者提供。点

# 实验性

在地图创建过程中处理数据集、产品或中间层可能非常繁琐。我花了很长时间寻找最佳的颜色和大小组合，以正确传达信息。有一次，我想表示从步行网络到图书馆的距离。我找到了一个非常吸引人的调色板用于地图，但显然它并没有传达出正确的信息。

在那一刻，我意识到并不是每张“看起来不错”的地图都是做得好的，因此我把它称作实验性可视化。

这个实验是在QGIS中完成的。

![](../Images/33abcc1bf1ab12d5afcc2a920e452ea2.png)

图片由作者提供。实验性。

如果你想了解更多关于我的地图和编码教程，请随时联系我。

> 💼 [Bryan R Vallejo LinkedIn](https://www.linkedin.com/in/bryanrvallejo/)
