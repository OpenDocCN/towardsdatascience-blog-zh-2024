# 为什么你不需要JS来制作3D图表

> 原文：[https://towardsdatascience.com/la-crime-now-in-3d-no-glasses-required-498398c25a39?source=collection_archive---------2-----------------------#2024-06-01](https://towardsdatascience.com/la-crime-now-in-3d-no-glasses-required-498398c25a39?source=collection_archive---------2-----------------------#2024-06-01)

## 在Python中可视化犯罪地理数据

[](https://medium.com/@alexroz?source=post_page---byline--498398c25a39--------------------------------)[![Aleksei Rozanov](../Images/748b69bfaccf39c9aa568a9e6f41eec3.png)](https://medium.com/@alexroz?source=post_page---byline--498398c25a39--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--498398c25a39--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--498398c25a39--------------------------------) [Aleksei Rozanov](https://medium.com/@alexroz?source=post_page---byline--498398c25a39--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--498398c25a39--------------------------------) ·6分钟阅读·2024年6月1日

--

![](../Images/41f0fd59d6221d6335f1d9b4c92ba959.png)

图片来自 [作者](https://medium.com/@alexroz)。

在我看来，地理数据最棒的特点之一就是它能够进行3D可视化。然而，由于进行此类计算所需的大量计算资源，这在Python中是非常罕见的（通常*JavaScript*及其相关库被用作替代方案）。在我之前的一篇文章中，我分享了六个Python包，可以创建美丽的静态和交互式地图，但仅限于2D空间。

[](https://medium.com/@alexroz/6-python-libraries-to-make-beautiful-maps-9fb9edb28b27?source=post_page-----498398c25a39--------------------------------) [## 6种用于制作美丽地图的Python库

### 每个数据科学家都会在某个时刻面临分析或建模地理空间数据的需求，而这无法在没有...

medium.com](https://medium.com/@alexroz/6-python-libraries-to-make-beautiful-maps-9fb9edb28b27?source=post_page-----498398c25a39--------------------------------)

今天，我想弥合这个差距，与大家一起探讨一个非常时尚且高效的框架，用于高性能的基于Web的可视化[**deck.jl**](https://deck.gl/)，它也有一个Python库 **PyDeck**。

要在Python中充分探索其功能，我们需要一个大型的地理空间数据集。一个理想的候选数据集是[Kaggle上的洛杉矶犯罪数据2010-2020](https://www.kaggle.com/datasets/sumaiaparveenshupti/los-angeles-crime-data-20102020)数据集。幸运的是，它具有[开放许可证](https://creativecommons.org/publicdomain/zero/1.0/)，因此我们可以自由使用它来进行我们的分析。

作者们分发了两个 *csv* 文件，我们将把它们合并为一个，同时过滤掉所有除了经纬度（即犯罪发生地点的坐标）以外的列。

**🐍完整的 Python 代码：** [**GitHub**](https://github.com/alexxxroz/Medium/blob/main/PyDeck_3D_Maps.ipynb)**.**

```py
import numpy as np
import pandas as pd
import xarray as xr
import geopandas as gpd
import random
import math

import matplotlib.pyplot as plt
from shapely import Point

import cartopy
import cartopy.crs as ccrs
import cartopy.feature as cfeature

import warnings

warnings.filterwarnings('ignore')

recent = pd.read_csv('./LA Crimes/Crime_Data_from_2020_to_Present.csv')[['LAT', 'LON']]
old = pd.read_csv('./LA Crimes/Crime_Data_from_2010_to_2019.csv')[['LAT', 'LON']]
df = pd.concat([old, recent])
df = df[(df.LON!=0) & (df.LAT!=0)] #zeros are Nans according to meta info
```

在 pandas 中加载数据后，我想使用 *cartopy* 做一个静态的二维可视化，只是为了有一个可靠的参考。如果我们仅仅绘制数据，我们将得到一堆数据点，对我们没有任何帮助。

![](../Images/e4c1d339c756c6b642af6d8ab0088150.png)

图片来源：[作者](https://medium.com/@alexroz)。

相反，让我们使用 NN 方法进行空间插值（你可以在我的另一篇文章中阅读更多关于它的内容）。

[](/3-best-methods-for-spatial-interpolation-912cab7aee47?source=post_page-----498398c25a39--------------------------------) [## 3 种最佳空间插值方法

### 从头开始，在 Python 中

[towardsdatascience.com](/3-best-methods-for-spatial-interpolation-912cab7aee47?source=post_page-----498398c25a39--------------------------------)

基本上，这意味着我们将稀疏的观测数据转化为一个地理网格（PyDeck 将做同样的事情，在这种情况下可以称之为数据聚合）。

```py
def coords(x,y, base=0.01):
 x, y = round(base * math.ceil(abs(x)/base),2), round(base * math.ceil(y/base),2)
 return (y,x)

def NN(data, LAT, LON):
  array = np.zeros((LAT.shape[0], LON.shape[0]),dtype=int)
  onGrid = data.apply(lambda row: coords(row.LAT, row.LON, 0.01), axis = 1).value_counts()
  for coor in onGrid.index:
    lon_idx, lat_idx = np.where(LON==coor[0]), np.where(LAT==coor[1])
    array[lat_idx,lon_idx] = int(onGrid[coor])
  return array
```

算法完成后（你需要等一段时间，因为我们有超过 200 万行数据要处理），我们可以将其结果包装成 *xarray* 数据集并进行映射。

```py
LAT, LON = np.arange(round(df.LAT.min()), round(df.LAT.max()), 0.01).astype(np.float32), np.arange(round(df.LON.min()), round(df.LON.max()), 0.01).astype(np.float32)
crimes = NN(df, LAT, LON)
ds = xr.Dataset(
    {'Crimes': (['lat', 'lon'], crimes)},
    coords={'lat': LAT, 'lon': LON})

fig, ax = plt.subplots(subplot_kw=dict(projection=ccrs.PlateCarree()), figsize=(16, 9))

ds.Crimes.plot(ax=ax, cmap='Reds')
ax.set_extent([-118.9, -118.1, 33.6, 34.5 ], crs=ccrs.PlateCarree())
ax.gridlines(draw_labels=True,linewidth=2, color='black', alpha=0.5, linestyle='--')
ax.add_feature(cfeature.BORDERS, edgecolor='black', linewidth=1)

ax.add_feature(cfeature.COASTLINE, edgecolor='black', linewidth=1)
ax.add_feature(cartopy.feature.RIVERS, edgecolor='blue', linewidth=0.5)
states_provinces = cfeature.NaturalEarthFeature(
            category='cultural',  name='admin_1_states_provinces',
            scale='10m', facecolor='none')

plt.show()
```

![](../Images/4cb04396ef5a88ff66debcc1afa1e262.png)

图片来源：[作者](https://medium.com/@alexroz)。

从我的角度来看，它看起来既不错又具有信息量，但如果你需要将这个项目推销给别人，使用这样的地图你很可能会失败 xD。所以，让我们来安装 PyDeck 并看看它能为我们做些什么！

# 1\. 六边形。

我发现最美的地图图层类型是六边形图层。在创建 PyDeck 的 L**ayer** 变量时你需要指定它。对于我们来说，还有一些其他至关重要的参数：

+   半径（六边形的半径，单位米，即空间分辨率，单位为米）；

+   elevation_scale（箱体的缩放因子，值越大，六边形越高）；

+   elevation_range（最小和最大高度）；

+   可选择（交互式显示值）；

+   extruded（单元格高度）。

```py
layer = pdk.Layer(
    'HexagonLayer',
    df,
    get_position=['LON', 'LAT'],
    radius=500, #bin radius
    auto_highlight=True,
    elevation_scale=50, #scale factor for bins (the greater - the higher)
    elevation_range=[0, 3000],
    pickable=True,
    extruded=True,#cell elevation
    )
```

我们需要创建的第二个变量是视图状态。我们需要为它提供：

+   经度和纬度；

+   缩放（初始缩放）；

+   min_zoom 和 max_zoom；

+   方位角（左/右视角）；

+   pitch（上下视角）。

```py
view_state = pdk.ViewState(
    longitude=-118.3,
    latitude=34.4,
    zoom=8,
    min_zoom=6,
    max_zoom=15,
    bearing=-20,#left/right angle
    pitch=20, #up/down angle
    )
```

由于我们有一个大数据集，Google Colab 并不会显示全部数据，因此你有两个选择：

+   从数据集中抽取 N 行样本；

+   将地图保存为 HTML 并在浏览器中打开。

如果你选择第二个，你将得到这个：

![](../Images/39e06c87e81077954efe4dc5f62ec734.png)

图片来源：[作者](https://medium.com/@alexroz)。

说实话，我喜欢六边形的外观，但在任何科学论文/报告/讲座中我都没见过它们，所以我建议大家有意识地使用它们。

# 2\. 列

现在让我们尝试创建一个类似的可视化，但是使用柱状图。但在这种情况下，我们需要将之前创建的xarray数据集传递给函数，并指定颜色和要可视化的变量：

```py
layer = pdk.Layer(
    'ColumnLayer',
    ds.to_dataframe().reset_index(),
    get_position=['lon', 'lat'],
    get_elevation='Crimes',
    elevation_scale=10,
    radius=200,
    get_fill_color=['Crimes', 220],
    pickable=True,
    extruded=True,
    )
```

![](../Images/cc30a98b5ee2146008744d31b6a2df27.png)

图片由[作者](https://medium.com/@alexroz)提供。

# 3\. 散点图

本质上，散点图是由点云组成的，但PyDeck的作者们开发了独特的圆柱体，它们看起来非常与众不同：

![](../Images/02f97b260ea3c59ba0ad2e115cea005f.png)

图片由[作者](https://medium.com/@alexroz)提供。

```py
layer = pdk.Layer(
    'ColumnLayer',
    df[:15000],
    get_position=['LON', 'LAT'],
    auto_highlight=True,
    get_radius=200,          # Radius is given in meters
    get_fill_color=[180, 0, 200, 140],  # Set an RGBA value for fill
    pickable=True)
```

![](../Images/b181b49d8fcccb41ab7b2117138ad0d5.png)

图片由[作者](https://medium.com/@alexroz)提供。

# 4\. 地图样式

PyDeck的一个非常酷的特点是，像plotly、geemap、folium等交互式映射工具一样，它允许用户更改底图，这意味着你可以根据项目的背景设计地图：

```py
r = pdk.Deck(layers=[layer],
             initial_view_state=view_state,
             map_style=pdk.map_styles.LIGHT, # ‘light’, ‘dark’, ‘road’, ‘satellite’, ‘dark_no_labels’, and ‘light_no_labels
             )
```

![](../Images/50b2a8f66e10ec360688621211efbdd0.png)

图片由[作者](https://medium.com/@alexroz)提供。

# 5\. 标题

下一个我认为非常有用的特性是改变交互式数据描述。通过将光标放在某个柱状图/六边形/点上，你可以获取元数据信息，但有时看起来有些荒谬。但在PyDeck中，你可以轻松克服这一点：

![](../Images/582e1ea8485234ef1a68a63fe9fa529c.png)

图片由[作者](https://medium.com/@alexroz)提供。

```py
r = pdk.Deck(layers=[layer],
             initial_view_state=view_state,
                  "html": "<b>Number of crimes:</b> {elevationValue}",
                  "style": {
                        "backgroundColor": "yellow",
                        "color": "black"
                  }
                },
             )
```

![](../Images/8ea69b8f10ab3eed20ae63fcbfcfb08b.png)

图片由[作者](https://medium.com/@alexroz)提供。

# 6\. 视角

最后，这个库最惊人的特性就是你可以通过简单地点击鼠标右键来改变地图的视角：

```py
from ipywidgets import HTML

text = HTML(value='Move the viewpoint')

def filter_by_bbox(row, west_lng, east_lng, north_lat, south_lat):
    return west_lng < row['lng'] < east_lng and south_lat < row['lat'] < north_lat

def filter_by_viewport(widget_instance, payload):
    west_lng, north_lat = payload['data']['nw']
    east_lng, south_lat = payload['data']['se']
    filtered_df = df[df.apply(lambda row: filter_by_bbox(row, west_lng, east_lng, north_lat, south_lat), axis=1)]

r.deck_widget.on_click(filter_by_viewport)
```

![](../Images/0d126df99ce2a038c2d6a607ca604a79.png)

图片由[作者](https://medium.com/@alexroz)提供。

我确实很喜欢**PyDeck**，并计划深入了解**deck.jl**框架。它的语法既简单又直观，能够让用户在保证能效的前提下构建出令人印象深刻的可视化。Python在一定程度上限制了这个包的功能，所以你可以查看他们的[画廊](https://deck.gl/examples)，真的非常震撼，尤其是他们的实验性GlobalView功能…

希望这篇文章对你有启发并且信息量十足！

===========================================

***我在Medium上的所有出版物都是免费且开放获取的，因此如果你能在这里关注我，我将非常感激！***

备注：我对（地理）数据科学、机器学习/人工智能以及气候变化充满极大的热情。如果你想一起合作进行某个项目，请通过[LinkedIn](https://www.linkedin.com/in/alexxxroz/)与我联系。

🛰️关注我，获取更多更新🛰️
