# UEFA 欧洲杯 2024 地图

> 原文：[https://towardsdatascience.com/uefa-euro-2024-map-9df58d8bd29b?source=collection_archive---------3-----------------------#2024-06-17](https://towardsdatascience.com/uefa-euro-2024-map-9df58d8bd29b?source=collection_archive---------3-----------------------#2024-06-17)

![](../Images/bc1f5c392be9290e6faa3cd1e9fe63c4.png)

## UEFA 欧洲锦标赛历史成绩记录

[](https://medium.com/@janosovm?source=post_page---byline--9df58d8bd29b--------------------------------)[![米兰·贾诺索夫](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--9df58d8bd29b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9df58d8bd29b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9df58d8bd29b--------------------------------) [米兰·贾诺索夫](https://medium.com/@janosovm?source=post_page---byline--9df58d8bd29b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9df58d8bd29b--------------------------------) ·6分钟阅读·2024年6月17日

--

现在，整个欧洲都在关注UEFA 欧洲杯 2024锦标赛。我们应该期待什么？各国球队之前表现如何？为了在地图上可视化这些信息，我将维基百科的数据和自然地球的世界地图结合，并使用Python中的地理空间数据科学工具，展示如何轻松构建可以与国家关联的任何数据的自定义地图。

*所有图片由作者提供。*

# 1. 数据获取

首先，我们使用来自[自然地球](https://www.naturalearthdata.com/downloads/10m-cultural-vectors/)计划的免费开源世界地图。为了确保每个国家都得到适当的表示，我使用了10米分辨率文化地图集中的*Admin 0 — Details — map units*地图。此地图涵盖了全球209个主权国家。

```py
# Import Geopandas
import geopandas as gpd

# Parse the world map
gdf = gpd.read_file('ne_10m_admin_0_map_units')
display(gdf[['GEOUNIT', 'geometry']].head(3))
gdf.plot()
```

输出结果：

![](../Images/b0695d0a69a30f477aaba26b9e994fcf.png)

接下来，我从[维基百科](https://en.wikipedia.org/wiki/UEFA_European_Championship_records_and_statistics#Comprehensive_team_results_by_tournament)收集官方的足球杯赛记录，我简单地复制并粘贴了这些内容……
