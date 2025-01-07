# 如何使用 DuckDB 读取 OSM 数据

> 原文：[https://towardsdatascience.com/how-to-read-osm-data-with-duckdb-ffeb15197390?source=collection_archive---------0-----------------------#2024-03-02](https://towardsdatascience.com/how-to-read-osm-data-with-duckdb-ffeb15197390?source=collection_archive---------0-----------------------#2024-03-02)

## 深入探索 OpenStreetMap 数据结构及其如何以可扩展的方式使用

[](https://raczeq.medium.com/?source=post_page---byline--ffeb15197390--------------------------------)[![Kamil Raczycki](../Images/2c45075e217e60660ad3b4475530333d.png)](https://raczeq.medium.com/?source=post_page---byline--ffeb15197390--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ffeb15197390--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ffeb15197390--------------------------------) [Kamil Raczycki](https://raczeq.medium.com/?source=post_page---byline--ffeb15197390--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ffeb15197390--------------------------------) ·29 分钟阅读·2024年3月2日

--

![](../Images/1b9c4439391aa4851cbe3131de09a318.png)

Dall-E 3 图像：一只可爱且迷人的 3D 渲染鸭子正在研究纸质地图，明亮的天空，模糊的背景，高质量，8k

本文将深入探讨如何使用 DuckDB 数据库读取 OpenStreetMap 数据。

本指南中描述的步骤将允许读者使用 Monaco 示例加载 OSM 数据，并将数据分为节点、路径和关系。

![](../Images/d0ebfd971420ab909f885f939cc1ca64.png)![](../Images/0ff4cf3693bfcbf785e16967120a527e.png)![](../Images/05b73197def9157df492056b089d8d72.png)

使用 DuckDB 引擎读取 OSM 元素的最终结果。从左至右：节点、路径和关系。由作者使用 GeoPandas 库生成。

为了完全理解本教程中描述的步骤，预期具备 SQL 语言的基本知识。大多数与 GIS 相关的操作和特殊连接将在文章中进一步描述。

## 文章大纲

+   什么是 OSM？——OpenStreetMap 服务简介。

+   OpenStreetMap 数据模型——定义了在 OSM 中使用的对象。

+   读取 OSM 数据——使用 DuckDB 对数据进行基本操作。

+   从**节点**中构建点几何体

+   从**路径**中构建线段和多边形几何体

+   从**关系**中构建多边形和多重多边形几何体

+   定义不规范的关系对象示例

+   QuackOSM —— 一款轻松读取 OSM 数据的工具

# 什么是 OSM？

OpenStreetMap（[OSM](https://openstreetmap.org/)）是全球最受欢迎的免费地图，并由日益增长的志愿者和贡献者群体持续维护。

社区收集并构建的数据可以公开免费用于商业目的，因此许多公司、学术研究人员和个人开发者都在他们的项目中使用这些资源。所有数据都遵循[开放数据公共开源数据库许可证](https://opendatacommons.org/licenses/odbl/1.0/)（ODbL）。

数据可以通过多种方式访问：

+   使用Overpass API（通过[Overpass Turbo](https://overpass-turbo.eu/)的Web GUI）

+   下载完整数据作为[Planet OSM](https://planet.openstreetmap.org/)（2024年当前超过70GB）

+   较小的下载提取： [Geofabrik](https://download.geofabrik.de/), [BBBike](https://extract.bbbike.org/), [OpenStreetMap.fr](https://download.openstreetmap.fr/), [Protomaps](https://app.protomaps.com/)

数据存储的最节省空间的文件类型是Protocolbuffer二进制格式，扩展名为`*.osm.pbf`。你可以[在这里](https://wiki.openstreetmap.org/wiki/PBF_Format)了解更多信息。

你还可以阅读[Eugenia Anello](https://medium.com/u/86fdc517c278?source=post_page---user_mention--ffeb15197390--------------------------------)关于OpenStreetMap的简短文章：

[](/a-comprehensive-guide-for-getting-started-with-openstreetmap-e92dff95fc80?source=post_page-----ffeb15197390--------------------------------) [## OpenStreetMap入门指南

### 在使用网站时学习OpenStreetMap的基本概念

towardsdatascience.com](/a-comprehensive-guide-for-getting-started-with-openstreetmap-e92dff95fc80?source=post_page-----ffeb15197390--------------------------------)

# OpenStreetMap数据模型

> 本节内容基于关于元素的[OSM Wiki页面](https://wiki.openstreetmap.org/wiki/Elements)

从概念上讲，OpenStreetMap中的数据分为3个组件：

节点表示空间中的点。它们通过WGS84坐标参考系统中的一对坐标表示——经度和纬度。节点可以用来定义地图上的单一特征（例如：长椅、路灯、树木），或者与其他节点一起用来表示路径的形状。

![](../Images/d1b8a31585dcda83fce64f09a495b165.png)

一个节点的示例——公园里的树。来自OpenStreetMap的截图，作者提供。

路径是通过使用**有序**节点列表表示的折线形状。这些折线可以是开放的，表示道路、运河和墙壁，或者它们可以闭合形成多边形，表示建筑物、森林、湖泊或其他简单形状。

![](../Images/66e199dab95b9bcb14552b0375443a37.png)

一个路径的示例——一部分高速公路。来自OpenStreetMap的截图，作者提供。

关系表示OSM中多个对象和数据元素之间的关系。例如，这可以是一个公交路线，其中路线表示公交行驶的道路，节点表示路线的站点，或者是一个由至少2个路径表示的带孔的多边形：这些可以是*外部*多边形和*内部*多边形。

![](../Images/638f80e316ca63427f0d70bd3f89cf98.png)

一个关系的示例——一个带有孔洞的酒店建筑轮廓。截图来自作者的OpenStreetMap。

每个元素可以，也不一定，附带**标签**。标签描述元素的含义。标签由键和值组成。没有固定的值字典，但用户应遵循OSM Wiki中记录的约定。

此外，每个元素都有一个在特定元素类型空间中唯一的ID（因此可能存在一个ID为100的节点、ID为100的路径和ID为100的关系）。

# 读取OSM数据

许多工具允许用户将OSM数据模型转换为在GIS领域常用的文件格式，例如[GDAL](https://gdal.org/drivers/vector/osm.html)。这些工具会自动从原始数据中重建几何形状。我们将尝试手动读取并重建几何形状。

> 以下示例展示了如何访问原始数据，并使用[DuckDB](https://duckdb.org/)引擎和[Spatial](https://duckdb.org/docs/extensions/spatial.html)扩展以SQL编写。带有完整Jupyter笔记本的所有查询可以在GitHub代码库中访问。
> 
> 你可以在并行环境中运行此笔记本，或者你可以[安装DuckDB引擎](https://duckdb.org/#quickinstall)，并打开CLI在其中执行查询。

[](https://github.com/RaczeQ/medium-articles/blob/main/articles/osm-duckdb/code.ipynb?source=post_page-----ffeb15197390--------------------------------) [## medium-articles/articles/osm-duckdb/code.ipynb at main · RaczeQ/medium-articles

### 用于Medium文章中代码和数据的代码库 - medium-articles/articles/osm-duckdb/code.ipynb at main ·…

github.com](https://github.com/RaczeQ/medium-articles/blob/main/articles/osm-duckdb/code.ipynb?source=post_page-----ffeb15197390--------------------------------)

## 获取数据

为了简便并便于访问，示例完全聚焦于摩纳哥地区。你可以从Geofabrik下载服务器下载当前的提取数据：[https://download.geofabrik.de/europe/monaco.html](https://download.geofabrik.de/europe/monaco.html)（点击`monaco-latest.osm.pbf`下载链接）

## 熟悉数据结构

首先，我们将使用`DESCRIBE`函数获取有关列的信息：

```py
DESCRIBE SELECT * FROM ST_READOSM('monaco-latest.osm.pbf');

┌─────────────┬──────────────────────────────────────────────┬─────────┬─────────┬─────────┬─────────┐
│ column_name │                 column_type                  │  null   │   key   │ default │  extra  │
│   varchar   │                   varchar                    │ varchar │ varchar │ varchar │ varchar │
├─────────────┼──────────────────────────────────────────────┼─────────┼─────────┼─────────┼─────────┤
│ kind        │ ENUM('node', 'way', 'relation', 'changeset') │ YES     │ NULL    │ NULL    │ NULL    │
│ id          │ BIGINT                                       │ YES     │ NULL    │ NULL    │ NULL    │
│ tags        │ MAP(VARCHAR, VARCHAR)                        │ YES     │ NULL    │ NULL    │ NULL    │
│ refs        │ BIGINT[]                                     │ YES     │ NULL    │ NULL    │ NULL    │
│ lat         │ DOUBLE                                       │ YES     │ NULL    │ NULL    │ NULL    │
│ lon         │ DOUBLE                                       │ YES     │ NULL    │ NULL    │ NULL    │
│ ref_roles   │ VARCHAR[]                                    │ YES     │ NULL    │ NULL    │ NULL    │
│ ref_types   │ ENUM('node', 'way', 'relation')[]            │ YES     │ NULL    │ NULL    │ NULL    │
└─────────────┴──────────────────────────────────────────────┴─────────┴─────────┴─────────┴─────────┘
```

`ST_READOSM`函数返回8个列：

+   kind —— 这是元素的类型。它也可以具有 `changeset` 的值，表示在编辑现有元素后所做的更改。Geofabrik下载服务器的提取数据不包含changeset，因此我们不需要考虑它们。

+   id —— 元素的标识符。

+   tags —— 一个由两个字符串组成的映射（或字典）：一个标签键和一个标签值。

+   refs —— 与元素相关的成员ID列表。节点应将此列表留空，路径和关系不能为空。

+   lat 和 lon —— 节点的纬度和经度。路径和关系应该将这些字段留空，因为在OSM中只有节点可以拥有坐标。

+   ref_roles 和 ref_types — 关于成员的附加信息列表：成员被分配的角色是什么，它属于什么类型（节点、道路或关系）。

## 计数元素数量。

让我们来看看总共有多少元素，以及每种元素类型的数量。

```py
SELECT COUNT(*) as total_elements
FROM ST_READOSM('monaco-latest.osm.pbf');

┌────────────────┐
│ total_elements │
│     int64      │
├────────────────┤
│          35782 │
└────────────────┘

SELECT kind, COUNT(*) as total_elements
FROM ST_READOSM('monaco-latest.osm.pbf')
GROUP BY 1;

┌──────────────────────────────────────────────┬────────────────┐
│                     kind                     │ total_features │
│ enum('node', 'way', 'relation', 'changeset') │     int64      │
├──────────────────────────────────────────────┼────────────────┤
│ node                                         │          30643 │
│ way                                          │           4849 │
│ relation                                     │            290 │
└──────────────────────────────────────────────┴────────────────┘
```

## 查看元素。

让我们检查每种元素类型的数据示例。

```py
SELECT *
FROM ST_READOSM('monaco-latest.osm.pbf')
WHERE kind = 'node'
LIMIT 5;

┌──────────────────────┬──────────┬─────────────────────────────┬─────────┬────────────────────┬────────────────────┬───────────┬───────────────────────────────────┐
│         kind         │    id    │            tags             │  refs   │        lat         │        lon         │ ref_roles │             ref_types             │
│ enum('node', 'way'…  │  int64   │    map(varchar, varchar)    │ int64[] │       double       │       double       │ varchar[] │ enum('node', 'way', 'relation')[] │
├──────────────────────┼──────────┼─────────────────────────────┼─────────┼────────────────────┼────────────────────┼───────────┼───────────────────────────────────┤
│ node                 │ 21911883 │                             │         │ 43.737117500000004 │  7.422909300000001 │           │                                   │
│ node                 │ 21911886 │ {crossing=zebra, crossing…  │         │ 43.737239900000006 │  7.423498500000001 │           │                                   │
│ node                 │ 21911894 │                             │         │ 43.737773100000005 │          7.4259193 │           │                                   │
│ node                 │ 21911901 │                             │         │ 43.737762100000005 │ 7.4267099000000005 │           │                                   │
│ node                 │ 21911908 │                             │         │         43.7381906 │           7.426961 │           │                                   │
└──────────────────────┴──────────┴─────────────────────────────┴─────────┴────────────────────┴────────────────────┴───────────┴───────────────────────────────────┘

SELECT *
FROM ST_READOSM('monaco-latest.osm.pbf')
WHERE kind = 'way'
LIMIT 5;

┌──────────────────────┬─────────┬──────────────────────┬─────────────────────────────────────────┬────────┬────────┬───────────┬───────────────────────────────────┐
│         kind         │   id    │         tags         │                  refs                   │  lat   │  lon   │ ref_roles │             ref_types             │
│ enum('node', 'way'…  │  int64  │ map(varchar, varch…  │                 int64[]                 │ double │ double │ varchar[] │ enum('node', 'way', 'relation')[] │
├──────────────────────┼─────────┼──────────────────────┼─────────────────────────────────────────┼────────┼────────┼───────────┼───────────────────────────────────┤
│ way                  │ 4097656 │ {highway=secondary…  │ [21912089, 7265761724, 1079750744, 21…  │        │        │           │                                   │
│ way                  │ 4098197 │ {highway=tertiary,…  │ [21918500, 10723812919, 1204288480, 2…  │        │        │           │                                   │
│ way                  │ 4224972 │ {highway=residenti…  │ [25177418, 4939779378, 4939779381, 49…  │        │        │           │                                   │
│ way                  │ 4224978 │ {access=no, addr:c…  │ [25178088, 25178091, 25178045, 251780…  │        │        │           │                                   │
│ way                  │ 4226740 │ {highway=secondary…  │ [25192130, 6444966483, 1737389127, 64…  │        │        │           │                                   │
└──────────────────────┴─────────┴──────────────────────┴─────────────────────────────────────────┴────────┴────────┴───────────┴───────────────────────────────────┘

SELECT *
FROM ST_READOSM('monaco-latest.osm.pbf')
WHERE kind = 'relation'
LIMIT 5;

┌──────────────────────┬───────┬──────────────────────┬──────────────────────┬────────┬────────┬──────────────────────┬─────────────────────────────────────────────┐
│         kind         │  id   │         tags         │         refs         │  lat   │  lon   │      ref_roles       │                  ref_types                  │
│ enum('node', 'way'…  │ int64 │ map(varchar, varch…  │       int64[]        │ double │ double │      varchar[]       │      enum('node', 'way', 'relation')[]      │
├──────────────────────┼───────┼──────────────────────┼──────────────────────┼────────┼────────┼──────────────────────┼─────────────────────────────────────────────┤
│ relation             │  7385 │ {ISO3166-2=FR-06, …  │ [1701090139, 32665…  │        │        │ [admin_centre, lab…  │ [node, node, way, way, way, way, way, way…  │
│ relation             │  8654 │ {ISO3166-2=FR-PAC,…  │ [26761400, 1251610…  │        │        │ [admin_centre, lab…  │ [node, node, way, way, way, way, way, way…  │
│ relation             │ 11980 │ {ISO3166-1=FR, ISO…  │ [17807753, 1363947…  │        │        │ [admin_centre, lab…  │ [node, node, relation, relation, relation…  │
│ relation             │ 36990 │ {ISO3166-1=MC, ISO…  │ [1790048269, 77077…  │        │        │ [admin_centre, out…  │ [node, way, way, way, way, way, way, way,…  │
│ relation             │ 90352 │ {admin_level=2, bo…  │ [770774507, 377944…  │        │        │ [outer, outer, out…  │ [way, way, way, way, way, way, way, way, …  │
└──────────────────────┴───────┴──────────────────────┴──────────────────────┴────────┴────────┴──────────────────────┴─────────────────────────────────────────────┘
```

现在我们可以看到元素的定义：节点有坐标，道路有填充了节点ID的*refs*列表，关系有最复杂的结构，*refs*列表填充了ID，*ref_types*列表显示哪个ID对应哪个元素类型。此外，*ref_roles*包含关于成员角色的信息（admin_centre，label，inner，outer）。

# 从节点中构建点几何形状。

现在我们知道结构是什么样的，我们可以开始构建一些几何形状了。从节点开始应该是最简单的，因为它仅仅是WGS84坐标参考系统中的一对纬度和经度。

我们应该只提取至少附带一个标签的节点，因为这些节点在分析中有语义意义。没有任何标签的节点可能会在后续阶段用于构建道路。

```py
SELECT
    id,
    tags,
    ST_POINT(lon, lat) geometry
FROM ST_READOSM('monaco-latest.osm.pbf')
WHERE kind = 'node'
    AND tags IS NOT NULL
    AND cardinality(tags) > 0;

┌─────────────┬─────────────────────────────────────────────────────────┬──────────────────────────────────────────────┐
│     id      │                          tags                           │                   geometry                   │
│    int64    │                  map(varchar, varchar)                  │                   geometry                   │
├─────────────┼─────────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│    21911886 │ {crossing=zebra, crossing:island=no, crossing_ref=zeb…  │ POINT (7.423498500000001 43.737239900000006) │
│    21912962 │ {crossing=zebra, crossing_ref=zebra, highway=crossing}  │ POINT (7.426912100000001 43.737912800000004) │
│    21914341 │ {crossing=uncontrolled, crossing_ref=zebra, highway=c…  │ POINT (7.4233732 43.737010000000005)         │
│    21915639 │ {highway=traffic_signals}                               │ POINT (7.4256003 43.7404449)                 │
│    21917308 │ {bus=yes, name=Monte-Carlo (Casino), public_transport…  │ POINT (7.4259854 43.740984700000006)         │
│    21918329 │ {crossing=marked, crossing:markings=yes, highway=cros…  │ POINT (7.427889100000001 43.7423616)         │
│    21918589 │ {crossing=marked, crossing:island=yes, crossing:marki…  │ POINT (7.4317478 43.7472774)                 │
│    21918697 │ {crossing=marked, crossing:island=no, crossing:markin…  │ POINT (7.432645000000001 43.747892900000004) │
│    21918939 │ {bus=yes, name=Portier, public_transport=stop_position} │ POINT (7.430429800000001 43.741472800000004) │
│    21919093 │ {crossing=marked, crossing:markings=yes, crossing_ref…  │ POINT (7.4352171 43.748160000000006)         │
│        ·    │                   ·                                     │                  ·                           │
│        ·    │                   ·                                     │                  ·                           │
│        ·    │                   ·                                     │                  ·                           │
│ 11450012980 │ {direction=forward, highway=give_way}                   │ POINT (7.416853100000001 43.735809700000004) │
│ 11450012981 │ {direction=forward, highway=give_way}                   │ POINT (7.416783000000001 43.735872900000004) │
│ 11450012982 │ {direction=forward, highway=give_way}                   │ POINT (7.416664900000001 43.735887000000005) │
│ 11450012983 │ {direction=forward, highway=give_way}                   │ POINT (7.4166968 43.7356945)                 │
│ 11451922579 │ {bus=yes, highway=bus_stop, name=Larvotto, public_tra…  │ POINT (7.435032400000001 43.7481639)         │
│ 11451922580 │ {bus=yes, highway=bus_stop, name=Grimaldi Forum, publ…  │ POINT (7.4311343 43.7442067)                 │
│ 11451922581 │ {bench=yes, bus=yes, highway=bus_stop, name=Portier, …  │ POINT (7.430357300000001 43.742209100000004) │
│ 11451922582 │ {bench=no, bin=no, bus=yes, highway=bus_stop, lit=yes…  │ POINT (7.4107674 43.7296193)                 │
│ 11451922600 │ {direction=backward, highway=give_way}                  │ POINT (7.4105622 43.7291648)                 │
│ 11452057060 │ {direction=backward, highway=give_way}                  │ POINT (7.419164 43.737116)                   │
├─────────────┴─────────────────────────────────────────────────────────┴──────────────────────────────────────────────┤
│ 3167 rows (20 shown)                                                                                       3 columns │
└──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

![](../Images/c940d87cb6db1a98fff59db98498049a.png)

节点在地图上的分布。由作者使用GeoPandas库生成。

在根据标签过滤节点后，我们剩下3167个点。

这大约占源文件中节点总数的10%。

# 从道路中构建线字符串和多边形几何形状。

让节点“出局” 😉，现在我们专注于道路。道路可以是线字符串或多边形。我们首先关注线字符串，然后再分配合适的几何类型。

为了构建道路，我们需要执行多个操作：

+   选择带有标签的匹配道路。

+   为每个道路元素展开所有节点引用，并保持它们的正确顺序（记住——节点引用的顺序很重要！）。

+   选择带有几何形状的所需节点。

+   按道路ID分组节点，并使用`ST_MakeLine`函数构建线字符串几何形状。

+   使用标签构建的几何形状连接。

我们从选择带有标签的道路开始。

```py
CREATE TEMP TABLE matching_ways AS 
SELECT id, tags
FROM ST_READOSM('monaco-latest.osm.pbf')
WHERE kind = 'way' AND tags IS NOT NULL AND cardinality(tags) > 0;

SELECT * FROM matching_ways;

┌────────────┬─────────────────────────────────────────────────────────────────┐
│     id     │                              tags                               │
│   int64    │                      map(varchar, varchar)                      │
├────────────┼─────────────────────────────────────────────────────────────────┤
│    4097656 │ {highway=secondary, lanes=2, lit=yes, maxspeed=30, name=Avenu…  │
│    4098197 │ {highway=tertiary, lanes=2, lit=yes, name=Boulevard d`Italie,…  │
│    4224972 │ {highway=residential, lit=yes, name=Avenue des Papalins, onew…  │
│    4224978 │ {access=no, addr:country=MC, leisure=pitch, lit=yes, sport=so…  │
│    4226740 │ {highway=secondary, lanes=2, lit=yes, maxspeed=50, name=Boule…  │
│    4227155 │ {area=yes, highway=pedestrian, lit=yes, name=Place du Palais,…  │
│    4227156 │ {access=no, bus=yes, demolished:highway=service, oneway=yes, …  │
│    4227157 │ {highway=residential, name=Rue des Remparts, oneway=yes, surf…  │
│    4227158 │ {highway=residential, name=Rue Philibert Florence, oneway=yes…  │
│    4227164 │ {highway=secondary, lanes=2, lit=yes, name=Virage Antony Nogu…  │
│       ·    │                                ·                                │
│       ·    │                                ·                                │
│       ·    │                                ·                                │
│ 1230247124 │ {bench=no, bin=yes, bus=yes, check_date=2023-12-10, covered=y…  │
│ 1230273018 │ {access=yes, leisure=playground, wheelchair=yes}                │
│ 1230273019 │ {highway=footway, surface=paving_stones}                        │
│ 1230398878 │ {abandoned:railway=rail, highway=primary, lanes=2, lit=yes, m…  │
│ 1233828725 │ {highway=service, lit=yes, name=Place Moulins, oneway=yes}      │
│ 1233828726 │ {highway=tertiary, junction=roundabout, lanes=2, lit=yes, non…  │
│ 1233853668 │ {highway=secondary, lanes=2, lit=yes, maxspeed=50, name=Boule…  │
│ 1238339144 │ {cycleway:left=lane, highway=tertiary, lanes=2, lit=yes, maxs…  │
│ 1238339145 │ {cycleway:left=lane, highway=tertiary, lanes=2, lit=yes, maxs…  │
│ 1239415633 │ {footway=sidewalk, highway=footway}                             │
├────────────┴─────────────────────────────────────────────────────────────────┤
│ 4786 rows (20 shown)                                               2 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

现在我们将展开引用列表，并将它们拆分成单独的行。我们还将利用DuckDB的索引功能来记住原始列表中元素的顺序。

```py
CREATE TEMP TABLE matching_ways_with_nodes_refs AS
SELECT id, UNNEST(refs) as ref, UNNEST(range(length(refs))) as ref_idx
FROM ST_READOSM('monaco-latest.osm.pbf')
SEMI JOIN matching_ways USING (id)
WHERE kind = 'way';

SELECT * FROM matching_ways_with_nodes_refs;

┌───────────┬────────────┬─────────┐
│    id     │    ref     │ ref_idx │
│   int64   │   int64    │  int64  │
├───────────┼────────────┼─────────┤
│   4097656 │   21912089 │       0 │
│   4097656 │ 7265761724 │       1 │
│   4097656 │ 1079750744 │       2 │
│   4097656 │ 2104793864 │       3 │
│   4097656 │ 6340961560 │       4 │
│   4097656 │ 1110560507 │       5 │
│   4097656 │   21912093 │       6 │
│   4097656 │ 6340961559 │       7 │
│   4097656 │   21912095 │       8 │
│   4097656 │ 7265762803 │       9 │
│      ·    │      ·     │       · │
│      ·    │      ·     │       · │
│      ·    │      ·     │       · │
│ 151154357 │ 7927855820 │       2 │
│ 151154358 │ 1868015912 │       0 │
│ 151154358 │ 1868015910 │       1 │
│ 151154358 │ 1868015900 │       2 │
│ 151154358 │ 4437858221 │       3 │
│ 151154358 │ 1868015889 │       4 │
│ 151154358 │ 1790048390 │       5 │
│ 151154358 │ 1868015881 │       6 │
│ 151154358 │ 1868015894 │       7 │
│ 151154358 │ 1868015896 │       8 │
├───────────┴────────────┴─────────┤
│  ? rows (>9999 rows, 20 shown)   │
└──────────────────────────────────┘
```

如你所见，现在每个道路元素都有多行数据，且有多个引用值。Ref_idx表示*refs*列表中原始顺序。

你还可以看到查询中的`SEMI JOIN`子句。这是DuckDB中特有的连接方式，它仅通过过滤行来代替实际的第二张表连接。你可以在官方文档中阅读更多信息：

[](https://duckdb.org/docs/sql/query_syntax/from?source=post_page-----ffeb15197390--------------------------------#semi-and-anti-joins) [## FROM & JOIN 子句

### FROM子句指定数据源，查询的其余部分将在该数据源上操作。从逻辑上看，...

[duckdb.org](https://duckdb.org/docs/sql/query_syntax/from?source=post_page-----ffeb15197390--------------------------------#semi-and-anti-joins)

现在我们可以根据上一阶段的 refs 选择所需的节点：

```py
CREATE TEMP TABLE required_nodes_with_geometries AS
SELECT id, ST_POINT(lon, lat) geometry
FROM ST_READOSM('monaco-latest.osm.pbf') nodes
SEMI JOIN matching_ways_with_nodes_refs
ON nodes.id = matching_ways_with_nodes_refs.ref
WHERE kind = 'node';

SELECT * FROM required_nodes_with_geometries;

┌────────────┬──────────────────────────────────────────────┐
│     id     │                   geometry                   │
│   int64    │                   geometry                   │
├────────────┼──────────────────────────────────────────────┤
│ 4547239708 │ POINT (7.4219302 43.7307441)                 │
│ 4547239709 │ POINT (7.421943700000001 43.730763)          │
│ 4547239710 │ POINT (7.421885100000001 43.7308797)         │
│ 4547239711 │ POINT (7.421869 43.730828200000005)          │
│ 4547239712 │ POINT (7.4218596 43.7307865)                 │
│ 4547239713 │ POINT (7.4219166 43.7307701)                 │
│ 4547239714 │ POINT (7.4220242 43.730742)                  │
│ 4547239715 │ POINT (7.4220765 43.730725500000005)         │
│ 4547239716 │ POINT (7.4220677 43.730680400000004)         │
│ 4547239717 │ POINT (7.422105200000001 43.730662)          │
│      ·     │                  ·                           │
│      ·     │                  ·                           │
│      ·     │                  ·                           │
│ 9983243582 │ POINT (7.427689600000001 43.732439400000004) │
│ 9983243583 │ POINT (7.4238021 43.7298837)                 │
│ 9983243584 │ POINT (7.4272774 43.7321503)                 │
│ 9983243585 │ POINT (7.4287133 43.7403807)                 │
│ 9983243586 │ POINT (7.429299500000001 43.740351100000005) │
│ 9983243587 │ POINT (7.4265819 43.7399094)                 │
│ 9983243588 │ POINT (7.4275342 43.7323072)                 │
│ 9983243589 │ POINT (7.427783700000001 43.732368)          │
│ 9983243590 │ POINT (7.428839600000001 43.7402718)         │
│ 9983243591 │ POINT (7.4269439 43.739707700000004)         │
├────────────┴──────────────────────────────────────────────┤
│ ? rows (>9999 rows, 20 shown)                   2 columns │
└───────────────────────────────────────────────────────────┘
```

现在我们可以构建完整的线字符串（linestring）：

```py
CREATE TEMP TABLE matching_ways_linestrings AS
SELECT
    matching_ways.id,
    matching_ways.tags,
    ST_MakeLine(list(nodes.geometry ORDER BY ref_idx ASC)) linestring
FROM matching_ways
JOIN matching_ways_with_nodes_refs
ON matching_ways.id = matching_ways_with_nodes_refs.id
JOIN required_nodes_with_geometries nodes
ON matching_ways_with_nodes_refs.ref = nodes.id
GROUP BY 1, 2;

SELECT * FROM matching_ways_linestrings;

┌────────────┬──────────────────────┬──────────────────────────────────────────┐
│     id     │         tags         │                linestring                │
│   int64    │ map(varchar, varch…  │                 geometry                 │
├────────────┼──────────────────────┼──────────────────────────────────────────┤
│    4227212 │ {highway=residenti…  │ LINESTRING (7.424979 43.73150620000000…  │
│    4227230 │ {access=yes, admin…  │ LINESTRING (7.4220576 43.7321018, 7.42…  │
│    4227273 │ {highway=secondary…  │ LINESTRING (7.4214168 43.7365583000000…  │
│    4229325 │ {highway=secondary…  │ LINESTRING (7.4201113 43.7373194000000…  │
│    4229900 │ {highway=secondary…  │ LINESTRING (7.4167819 43.7299324, 7.41…  │
│    4230116 │ {highway=footway, …  │ LINESTRING (7.4302666 43.7467818, 7.43…  │
│   24672725 │ {highway=residenti…  │ LINESTRING (7.4356746 43.7516752, 7.43…  │
│   25082701 │ {area=yes, highway…  │ LINESTRING (7.421602 43.7370201, 7.421…  │
│   31900562 │ {area=yes, floatin…  │ LINESTRING (7.422541900000001 43.73529…  │
│   37794470 │ {admin_level=2, bo…  │ LINESTRING (7.438411400000001 43.74942…  │
│       ·    │        ·             │                    ·                     │
│       ·    │        ·             │                    ·                     │
│       ·    │        ·             │                    ·                     │
│ 1203859766 │ {landuse=grass}      │ LINESTRING (7.4172849 43.7324023000000…  │
│ 1203859775 │ {landuse=grass}      │ LINESTRING (7.417039300000001 43.73209…  │
│ 1218054879 │ {highway=footway, …  │ LINESTRING (7.4257143 43.7307631000000…  │
│ 1229465608 │ {amenity=shelter, …  │ LINESTRING (7.417716100000001 43.73075…  │
│ 1230102086 │ {footway=crossing,…  │ LINESTRING (7.414346 43.72708160000000…  │
│ 1230123519 │ {footway=link, hig…  │ LINESTRING (7.4238967 43.7370229, 7.42…  │
│ 1202747620 │ {crossing=unmarked…  │ LINESTRING (7.420526100000001 43.72671…  │
│ 1203858289 │ {crossing=uncontro…  │ LINESTRING (7.4179021 43.7344211000000…  │
│ 1203861575 │ {amenity=parking}    │ LINESTRING (7.430036100000001 43.74015…  │
│ 1203861579 │ {landuse=construct…  │ LINESTRING (7.4323636 43.7423150000000…  │
├────────────┴──────────────────────┴──────────────────────────────────────────┤
│ 4786 rows (20 shown)                                               3 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

![](../Images/4ac75ad2615c915d36bbd5d22d94dbf3.png)

在地图上绘制的路线线字符串（linestring）。由作者使用 GeoPandas 库生成。

完成所有这些操作后，我们得到的是线字符串形式的路径。现在我们需要选择应该是多边形的路径。我们可以根据标签值来完成这项工作。不幸的是，对于此操作，并没有单一的权威来源。我们可以查看 OSM 维基页面，了解社区创建的其中一种定义。

[](https://wiki.openstreetmap.org/wiki/Overpass_turbo/Polygon_Features?source=post_page-----ffeb15197390--------------------------------) [## Overpass turbo/Polygon Features

### 由于 OpenStreetMap 没有固有的区域数据类型，因此必须应用启发式方法来确定一条路径是否是……

[wiki.openstreetmap.org](https://wiki.openstreetmap.org/wiki/Overpass_turbo/Polygon_Features?source=post_page-----ffeb15197390--------------------------------) ![](../Images/78cf5e001831f9276d433c6b61d252da.png)

来自 Overpass turbo/Polygon Features 维基页面的截图。拍摄于 2024-01-17。

如你所见，这个列表相当长，因此为了简洁起见，我们将仅检查线字符串是否形成闭合环路，以及区域标签值是否不是 'no'。

```py
WITH way_polygon_feature AS (
    SELECT
        id,
        (
            -- if first and last nodes are the same
            ST_Equals(ST_StartPoint(linestring), ST_EndPoint(linestring))
            -- if the element doesn't have any tags leave it as a Linestring
            AND tags IS NOT NULL
            -- if the element is specifically tagged 'area':'no' -> LineString
            AND NOT (
                list_contains(map_keys(tags), 'area')
                AND list_extract(map_extract(tags, 'area'), 1) = 'no'
            )
        ) AS is_polygon
    FROM matching_ways_linestrings
)
SELECT
    matching_ways_linestrings.id,
    matching_ways_linestrings.tags,
    (CASE
        WHEN is_polygon
        THEN ST_MakePolygon(linestring)
        ELSE linestring
    END)::GEOMETRY AS geometry
FROM matching_ways_linestrings
JOIN way_polygon_feature
ON matching_ways_linestrings.id = way_polygon_feature.id;

┌────────────┬──────────────────────┬──────────────────────────────────────────┐
│     id     │         tags         │                 geometry                 │
│   int64    │ map(varchar, varch…  │                 geometry                 │
├────────────┼──────────────────────┼──────────────────────────────────────────┤
│    4226740 │ {highway=secondary…  │ LINESTRING (7.422170500000001 43.73286…  │
│    4227198 │ {highway=residenti…  │ LINESTRING (7.420338900000001 43.73223…  │
│    4227216 │ {highway=service, …  │ LINESTRING (7.423448400000001 43.73268…  │
│    4227242 │ {highway=secondary…  │ LINESTRING (7.4221786 43.7322418000000…  │
│    4227272 │ {highway=secondary…  │ LINESTRING (7.422117500000001 43.73702…  │
│    4229292 │ {foot=no, highway=…  │ LINESTRING (7.41643 43.7311373, 7.4168…  │
│    4229577 │ {highway=residenti…  │ LINESTRING (7.4251499 43.7397216, 7.42…  │
│    4230122 │ {alt_name=rue R. P…  │ LINESTRING (7.428823400000001 43.74602…  │
│   25082741 │ {highway=secondary…  │ LINESTRING (7.4231414 43.7369886000000…  │
│   25722909 │ {highway=footway, …  │ LINESTRING (7.423659300000001 43.73134…  │
│       ·    │          ·           │                    ·                     │
│       ·    │          ·           │                    ·                     │
│       ·    │          ·           │                    ·                     │
│ 1089844256 │ {handrail=no, high…  │ LINESTRING (7.4229453 43.7326080000000…  │
│ 1089844296 │ {highway=footway}    │ LINESTRING (7.427017200000001 43.74044…  │
│ 1202570722 │ {access=private, l…  │ POLYGON ((7.4263241 43.7390763, 7.4263…  │
│  946167565 │ {disused:leisure=p…  │ POLYGON ((7.4276993 43.739335100000005…  │
│ 1089844271 │ {highway=footway}    │ LINESTRING (7.4277667 43.732816, 7.427…  │
│  335731716 │ {highway=footway}    │ LINESTRING (7.417764200000001 43.73439…  │
│  779454018 │ {building=resident…  │ POLYGON ((7.4217522 43.7277202, 7.4218…  │
│  943330855 │ {landuse=grass}      │ POLYGON ((7.4151844 43.7332252, 7.4152…  │
│ 1089844229 │ {highway=steps, in…  │ LINESTRING (7.4274525 43.7328052, 7.42…  │
│   49209608 │ {addr:city=Monaco,…  │ POLYGON ((7.4223448 43.7304076, 7.4221…  │
├────────────┴──────────────────────┴──────────────────────────────────────────┤
│ 4786 rows (20 shown)                                               3 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

现在我们可以在最终结果中看到线字符串（linestring）和多边形（polygon）几何体的混合。 当然，*is_polygon*列的谓词可以（甚至*应该*）通过上述提到的关于标签值的逻辑来扩展。

与上面的图片相比，现在地图上可见更多填充的多边形。

![](../Images/15582954eacc19644cb8eff1f018f0cb.png)

在地图上绘制的路线几何体。由作者使用 GeoPandas 库生成。

# 从关系中构建多边形和多多边形几何体

在 OSM 中，关系被用来将多个其他元素组合成一个单一的对象。这里我们将仅关注（多）多边形。这些特定的元素有一个 `type` 标签，其值为两者之一：`boundary` 和 `multipolygon`。

这种对象是最复杂的，需要重建几何体。以下是我们需要执行的步骤列表：

+   选择具有适当 `type` 值的关系。

+   将与关系相关的所有 refs 展开，并仅保留路径 refs —— 我们只需要相关的路径 refs 来重建多边形。

+   选择具有线字符串几何体的所需路径 —— 在这里我们可以利用构建路径的步骤。

+   如果路径的 ref 为 `null`，则将其分配为 ‘outer’ 角色，并检查关系中的任何 ref 是否具有 ‘outer’ 角色 —— 如果一个关系没有 ‘outer’ refs，则将它们全部视为 ‘outer’。

+   按“外部”和“内部”角色对所有线字符串进行分组，并将它们合并为一个单一的多线字符串——许多关系是由多个单一线字符串定义的，只有将它们组合起来才能形成一个闭合的多边形。

+   将多线字符串拆分为单一闭环线字符串，并将其保存为多边形。

+   将几何体拆分为“外部”和“内部”多边形。这些可以从关系对象的`ref_role`列中提取。

+   对于每个“外部”多边形，选择所有完全包含在其中的“内部”多边形，并在该多边形中创建孔。

+   对所有带孔的“外部”多边形进行联合。

让我们从选择具有匹配标签值的关系开始。

```py
CREATE TEMP TABLE matching_relations AS 
SELECT id, tags
FROM ST_READOSM('monaco-latest.osm.pbf')
WHERE kind = 'relation' AND len(refs) > 0
    AND tags IS NOT NULL AND cardinality(tags) > 0
    AND list_contains(map_keys(tags), 'type')
    AND list_has_any(map_extract(tags, 'type'), ['boundary', 'multipolygon']);

SELECT * FROM matching_relations;

┌──────────┬───────────────────────────────────────────────────────────────────┐
│    id    │                               tags                                │
│  int64   │                       map(varchar, varchar)                       │
├──────────┼───────────────────────────────────────────────────────────────────┤
│     7385 │ {ISO3166-2=FR-06, admin_level=6, alt_name:de=Meeralpen, border_…  │
│     8654 │ {ISO3166-2=FR-PAC, admin_level=4, border_type=region, boundary=…  │
│    36990 │ {ISO3166-1=MC, ISO3166-1:alpha2=MC, ISO3166-1:alpha3=MCO, admin…  │
│   174558 │ {admin_level=8, boundary=administrative, name=Roquebrune-Cap-Ma…  │
│   174562 │ {admin_level=8, boundary=administrative, name=Beausoleil, name:…  │
│   174956 │ {admin_level=8, alt_name:it=Capo d`Aglio, boundary=administrati…  │
│   174958 │ {admin_level=8, boundary=administrative, name=La Turbie, name:i…  │
│   393226 │ {building=castle, castle_type=palace, charge=10€, email=visites…  │
│   393481 │ {building=school, name=Pensionnat des Dames de Saint-Maur, type…  │
│  1124039 │ {ISO3166-1=MC, ISO3166-1:alpha2=MC, ISO3166-1:alpha3=MCO, ISO31…  │
│     ·    │                             ·                                     │
│     ·    │                             ·                                     │
│     ·    │                             ·                                     │
│ 14399505 │ {area=yes, floating=yes, man_made=pier, type=multipolygon}        │
│ 16248281 │ {building=apartments, name=F, type=multipolygon}                  │
│ 16248282 │ {building=apartments, name=E, type=multipolygon}                  │
│ 16248283 │ {building=apartments, name=D, type=multipolygon}                  │
│ 16248284 │ {building=apartments, name=C, type=multipolygon}                  │
│ 16248285 │ {building=apartments, name=B, type=multipolygon}                  │
│ 16248286 │ {building=apartments, name=A, type=multipolygon}                  │
│ 16250182 │ {addr:country=MC, alt_name=Parc de la Roseraie, leisure=park, n…  │
│ 16261416 │ {natural=water, type=multipolygon, water=pond}                    │
│ 16467322 │ {admin_level=2, boundary=land_area, land_area=administrative, n…  │
├──────────┴───────────────────────────────────────────────────────────────────┤
│ 78 rows (20 shown)                                                 2 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

现在我们将展开引用列表，并将其拆分为单独的行。与路径相似，我们也将利用DuckDB的索引功能来记住原始列表中元素的顺序。此外，我们只会保留`way`引用。

```py
CREATE TEMP TABLE matching_relations_with_ways_refs AS
WITH unnested_relation_refs AS (
    SELECT
        r.id,
        UNNEST(refs) as ref,
        UNNEST(ref_types) as ref_type,
        UNNEST(ref_roles) as ref_role,
        UNNEST(range(length(refs))) as ref_idx
    FROM ST_READOSM('monaco-latest.osm.pbf') r
    SEMI JOIN matching_relations USING (id)
    WHERE kind = 'relation'
)
SELECT id, ref, ref_role, ref_idx
FROM unnested_relation_refs
WHERE ref_type = 'way';

SELECT * FROM matching_relations_with_ways_refs;

┌──────────┬───────────┬──────────┬─────────┐
│    id    │    ref    │ ref_role │ ref_idx │
│  int64   │   int64   │ varchar  │  int64  │
├──────────┼───────────┼──────────┼─────────┤
│     7385 │  30836152 │ outer    │       2 │
│     7385 │ 889278953 │ outer    │       3 │
│     7385 │ 889278956 │ outer    │       4 │
│     7385 │ 889278957 │ outer    │       5 │
│     7385 │ 889278958 │ outer    │       6 │
│     7385 │ 889278962 │ outer    │       7 │
│     7385 │ 960087656 │ outer    │       8 │
│     7385 │  30836155 │ outer    │       9 │
│     7385 │ 889278965 │ outer    │      10 │
│     7385 │ 889278963 │ outer    │      11 │
│       ·  │      ·    │   ·      │       · │
│       ·  │      ·    │   ·      │       · │
│       ·  │      ·    │   ·      │       · │
│ 11278320 │  35150936 │ outer    │     197 │
│ 11278320 │ 466647567 │ outer    │     198 │
│ 11278320 │ 214008684 │ outer    │     199 │
│ 11278320 │ 466647569 │ outer    │     200 │
│ 11278320 │ 466647568 │ outer    │     201 │
│ 11278320 │ 214008689 │ outer    │     202 │
│ 11278320 │ 263776194 │ outer    │     203 │
│ 11278320 │  31124893 │ outer    │     204 │
│ 11278320 │  31124895 │ outer    │     205 │
│ 11278320 │  31124899 │ outer    │     206 │
├──────────┴───────────┴──────────┴─────────┤
│ ? rows (>9999 rows, 20 shown)   4 columns │
└───────────────────────────────────────────┘
```

在下一步中，我们将为关系所需的路径构建线字符串。下面的查询几乎完整地压缩了读取路径的逻辑（获取所需的节点，构建点并将其分组为线字符串）：

```py
CREATE TEMP TABLE required_ways_linestrings AS
WITH ways_required_by_relations_with_nodes_refs AS (
    SELECT id, UNNEST(refs) as ref, UNNEST(range(length(refs))) as ref_idx
    FROM ST_READOSM('monaco-latest.osm.pbf') ways
    SEMI JOIN matching_relations_with_ways_refs
    ON ways.id = matching_relations_with_ways_refs.ref
    WHERE kind = 'way'
),
nodes_required_by_relations_with_geometries AS (
    SELECT id, ST_POINT(lon, lat) geometry
    FROM ST_READOSM('monaco-latest.osm.pbf') nodes
    SEMI JOIN ways_required_by_relations_with_nodes_refs
    ON nodes.id = ways_required_by_relations_with_nodes_refs.ref
    WHERE kind = 'node'
)
SELECT
    ways.id,
    ST_MakeLine(list(nodes.geometry ORDER BY ref_idx ASC)) linestring
FROM ways_required_by_relations_with_nodes_refs ways
JOIN nodes_required_by_relations_with_geometries nodes
ON ways.ref = nodes.id
GROUP BY 1;

SELECT * FROM required_ways_linestrings;

┌────────────┬─────────────────────────────────────────────────────────────────┐
│     id     │                           linestring                            │
│   int64    │                            geometry                             │
├────────────┼─────────────────────────────────────────────────────────────────┤
│   37794470 │ LINESTRING (7.438411400000001 43.749422300000006, 7.4384056 4…  │
│   87878917 │ LINESTRING (7.418041100000001 43.7256933, 7.4181547 43.725613…  │
│   94252430 │ LINESTRING (7.4141873 43.729494100000004, 7.414203400000001 4…  │
│   94399618 │ LINESTRING (7.4239717 43.740543100000004, 7.4238252 43.740372…  │
│   94399736 │ LINESTRING (7.423881400000001 43.7402971, 7.4239411 43.740265…  │
│  104863462 │ LINESTRING (7.424840000000001 43.731281800000005, 7.424917000…  │
│  120114110 │ LINESTRING (7.420872 43.7370979, 7.4207787 43.7370534, 7.4206…  │
│  156242249 │ LINESTRING (7.4294728 43.739895600000004, 7.429579500000001 4…  │
│  165636038 │ LINESTRING (7.419717100000001 43.732398700000005, 7.419772900…  │
│  169297863 │ LINESTRING (7.422815300000001 43.7321967, 7.4234109 43.732263…  │
│       ·    │                                ·                                │
│       ·    │                                ·                                │
│       ·    │                                ·                                │
│   24874398 │ LINESTRING (7.418523400000001 43.7247599, 7.419012800000001 4…  │
│   92627424 │ LINESTRING (7.4166521 43.7322122, 7.4162303 43.73173910000000…  │
│   94452829 │ LINESTRING (7.4317066 43.7469647, 7.4317998 43.7470371, 7.431…  │
│  335740502 │ LINESTRING (7.4185151 43.7353633, 7.418442000000001 43.735298…  │
│  398377193 │ LINESTRING (7.420872 43.7370979, 7.420939000000001 43.7371298…  │
│  405529436 │ LINESTRING (7.417534900000001 43.7258914, 7.4175347 43.725833…  │
│  423632259 │ LINESTRING (7.4212208 43.7320944, 7.421461300000001 43.732057…  │
│  572935479 │ LINESTRING (7.427508100000001 43.7394168, 7.427496700000001 4…  │
│  586494707 │ LINESTRING (7.4270357 43.739109000000006, 7.4269512 43.739155…  │
│ 1202570715 │ LINESTRING (7.426448400000001 43.739491400000006, 7.4264682 4…  │
├────────────┴─────────────────────────────────────────────────────────────────┤
│ 141 rows (20 shown)                                                2 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

在创建所需的线字符串后，我们现在可以将它们与关系数据连接起来。我们还会确保正确解析所需的`ref_role` — 填充空值或替换它们，如果关系在OSM数据库中错误地定义了`ref_roles`。

```py
CREATE TEMP TABLE matching_relations_with_ways_linestrings AS
WITH unnested_relations_with_way_linestrings AS (
    SELECT
        r.id,
        COALESCE(r.ref_role, 'outer') as ref_role,
        r.ref,
        w.linestring::GEOMETRY as geometry
    FROM matching_relations_with_ways_refs r
    JOIN required_ways_linestrings w
    ON w.id = r.ref
    ORDER BY r.id, r.ref_idx
),
any_outer_refs AS (
    -- check if any way attached to the relation has the `outer` role
    SELECT id, bool_or(ref_role == 'outer') has_any_outer_refs
    FROM unnested_relations_with_way_linestrings
    GROUP BY id
)
SELECT
    unnested_relations_with_way_linestrings.* EXCLUDE (ref_role),
    -- if none of the way refs has `outer` role - treat each ref as `outer`
    CASE WHEN any_outer_refs.has_any_outer_refs
        THEN unnested_relations_with_way_linestrings.ref_role
        ELSE 'outer'
    END as ref_role
FROM unnested_relations_with_way_linestrings
JOIN any_outer_refs
ON any_outer_refs.id = unnested_relations_with_way_linestrings.id;

SELECT * FROM matching_relations_with_ways_linestrings;

┌──────────┬───────────┬────────────────────────────────────────────┬──────────┐
│    id    │    ref    │                  geometry                  │ ref_role │
│  int64   │   int64   │                  geometry                  │ varchar  │
├──────────┼───────────┼────────────────────────────────────────────┼──────────┤
│     7385 │ 772081595 │ LINESTRING (7.415291600000001 43.7234393…  │ outer    │
│     7385 │ 212810311 │ LINESTRING (7.4120416 43.7280955, 7.4123…  │ outer    │
│     7385 │ 176533407 │ LINESTRING (7.412670800000001 43.7316348…  │ outer    │
│     7385 │  37811853 │ LINESTRING (7.4127007 43.7346954, 7.4126…  │ outer    │
│     7385 │  37794471 │ LINESTRING (7.428754700000001 43.7460174…  │ outer    │
│     7385 │ 398372186 │ LINESTRING (7.436885 43.7519173, 7.43677…  │ outer    │
│     7385 │  37794470 │ LINESTRING (7.438411400000001 43.7494223…  │ outer    │
│     7385 │ 770774507 │ LINESTRING (7.439171000000001 43.7490109…  │ outer    │
│     8654 │ 772081595 │ LINESTRING (7.415291600000001 43.7234393…  │ outer    │
│     8654 │ 212810311 │ LINESTRING (7.4120416 43.7280955, 7.4123…  │ outer    │
│       ·  │     ·     │                     ·                      │   ·      │
│       ·  │     ·     │                     ·                      │   ·      │
│       ·  │     ·     │                     ·                      │   ·      │
│ 11546878 │ 840890844 │ LINESTRING (7.420754 43.735872900000004,…  │ outer    │
│ 11546878 │ 840890848 │ LINESTRING (7.4207413 43.7356673, 7.4207…  │ outer    │
│ 16467322 │ 772081595 │ LINESTRING (7.415291600000001 43.7234393…  │ outer    │
│ 16467322 │ 212810311 │ LINESTRING (7.4120416 43.7280955, 7.4123…  │ outer    │
│ 16467322 │ 176533407 │ LINESTRING (7.412670800000001 43.7316348…  │ outer    │
│ 16467322 │  37811853 │ LINESTRING (7.4127007 43.7346954, 7.4126…  │ outer    │
│ 16467322 │  37794471 │ LINESTRING (7.428754700000001 43.7460174…  │ outer    │
│ 16467322 │ 398372186 │ LINESTRING (7.436885 43.7519173, 7.43677…  │ outer    │
│ 16467322 │  37794470 │ LINESTRING (7.438411400000001 43.7494223…  │ outer    │
│ 16467322 │ 770774507 │ LINESTRING (7.439171000000001 43.7490109…  │ outer    │
├──────────┴───────────┴────────────────────────────────────────────┴──────────┤
│ 410 rows (20 shown)                                                4 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

如您所见，每个关系都分配了多个路径和线字符串。让我们通过一个示例来看它们在地图上的表现：

![](../Images/a34fe385da26c62cce2dd5d832df57a9.png)

一个单一的关系（[5986437](https://www.openstreetmap.org/relation/5986437)），其中包含按颜色编码的路径。由作者使用GeoPandas库生成。

要创建完整的多边形，我们必须利用`ST_LineMerge`函数，它将组合一系列线字符串（你可以将其比作将一段段绳子绑在一起）。你可以在这里阅读更多关于此操作的信息：

[](https://postgis.net/docs/ST_LineMerge.html?source=post_page-----ffeb15197390--------------------------------) [## ST_LineMerge

### ST_LineMerge - 返回通过缝合多个线字符串形成的线。

[postgis.net](https://postgis.net/docs/ST_LineMerge.html?source=post_page-----ffeb15197390--------------------------------)

作为额外的验证步骤，我们将检查生成的线字符串是否至少包含4个点，并且第一个点是否等于最后一个点，然后再将它们转换为多边形：

```py
CREATE TEMP TABLE matching_relations_with_merged_polygons AS
WITH merged_linestrings AS (
    SELECT
        id,
        ref_role,
        UNNEST(
            ST_Dump(ST_LineMerge(ST_Collect(list(geometry)))),
            recursive := true
        ),
    FROM matching_relations_with_ways_linestrings
    GROUP BY id, ref_role
),
relations_with_linestrings AS (
    SELECT
        id,
        ref_role,
        -- ST_Dump returns column named `geom`
        geom AS geometry,
        row_number() OVER (PARTITION BY id) as geometry_id
    FROM
        merged_linestrings
    -- discard linestrings with less than 4 points
    WHERE ST_NPoints(geom) >= 4
),
valid_relations AS (
    SELECT id, is_valid
    FROM (
        SELECT
            id,
            bool_and(
                -- Check if start point equals the end point
                ST_Equals(ST_StartPoint(geometry), ST_EndPoint(geometry))
            ) is_valid
        FROM relations_with_linestrings
        GROUP BY id
    )
    WHERE is_valid = true
)
SELECT 
    id,
    ref_role,
    ST_MakePolygon(geometry) geometry,
    geometry_id
FROM relations_with_linestrings
SEMI JOIN valid_relations
ON relations_with_linestrings.id = valid_relations.id;

SELECT * FROM matching_relations_with_merged_polygons;

┌──────────┬──────────┬──────────────────────────────────────────┬─────────────┐
│    id    │ ref_role │                 geometry                 │ geometry_id │
│  int64   │ varchar  │                 geometry                 │    int64    │
├──────────┼──────────┼──────────────────────────────────────────┼─────────────┤
│  1369631 │ inner    │ POLYGON ((7.438232200000001 43.7511057…  │           1 │
│  1369631 │ outer    │ POLYGON ((7.438008600000001 43.7502850…  │           2 │
│  1484217 │ outer    │ POLYGON ((7.423010700000001 43.7307028…  │           1 │
│  1484217 │ inner    │ POLYGON ((7.423114600000001 43.7305994…  │           2 │
│  5986436 │ outer    │ POLYGON ((7.428754700000001 43.7460174…  │           1 │
│  8269572 │ outer    │ POLYGON ((7.4287048 43.737701400000006…  │           1 │
│  8269572 │ inner    │ POLYGON ((7.4284492 43.7375604, 7.4286…  │           2 │
│ 16248286 │ outer    │ POLYGON ((7.426306200000001 43.7391565…  │           1 │
│ 16248286 │ inner    │ POLYGON ((7.4263241 43.7390763, 7.4263…  │           2 │
│ 16261416 │ inner    │ POLYGON ((7.417829200000001 43.731382,…  │           1 │
│     ·    │   ·      │                    ·                     │           · │
│     ·    │   ·      │                    ·                     │           · │
│     ·    │   ·      │                    ·                     │           · │
│  8280869 │ inner    │ POLYGON ((7.426753300000001 43.7388868…  │           2 │
│  8280869 │ inner    │ POLYGON ((7.4270357 43.739109000000006…  │           3 │
│  8280869 │ inner    │ POLYGON ((7.4271374 43.739011500000004…  │           4 │
│  8280869 │ inner    │ POLYGON ((7.4272666 43.7389165, 7.4273…  │           5 │
│ 11384697 │ outer    │ POLYGON ((7.427075 43.7315167, 7.42749…  │           1 │
│ 11384697 │ inner    │ POLYGON ((7.426917700000001 43.7313266…  │           2 │
│ 11384697 │ inner    │ POLYGON ((7.427001400000001 43.7313937…  │           3 │
│ 11546878 │ outer    │ POLYGON ((7.4207413 43.7356673, 7.4207…  │           1 │
│ 11538023 │ inner    │ POLYGON ((7.426528200000001 43.7329359…  │           1 │
│ 11538023 │ outer    │ POLYGON ((7.426554 43.7328276, 7.42654…  │           2 │
├──────────┴──────────┴──────────────────────────────────────────┴─────────────┤
│ 88 rows (20 shown)                                                 4 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

让我们看看执行此操作后的前一个示例。我们应该可以看到两个独立的多边形：

![](../Images/d72c2b5c71b7e6a1349da547f29bb068.png)

一个单一的关系（[5986437](https://www.openstreetmap.org/relation/5986437)），其中合并的路径作为两个独立的多边形。由作者使用GeoPandas库生成。

我之前提到过，创建关系的路径具有`outer`和`inner`的`ref_types`。这里你可以看到它们的样子：

![](../Images/4bf65f217dccd5aedeea10bf2b511cde.png)

一个单一关系（[8280869](https://www.openstreetmap.org/relation/828086)）与合并的路径分组为外部和内部多边形。由作者使用 GeoPandas 库生成。

这些角色意味着 `inner` 路径是 `outer` 多边形中的“孔洞”，我们必须重现这一步骤以确保几何体的正确性。

现在让我们专注于将多边形分为有孔和没有孔的两组，使用 `ST_Within` 谓词，它检查一个几何体是否完全位于另一个几何体内。

```py
CREATE TEMP TABLE matching_relations_with_outer_polygons_with_holes AS
WITH outer_polygons AS (
    SELECT id, geometry_id, geometry
    FROM matching_relations_with_merged_polygons
    WHERE ref_role = 'outer'
), inner_polygons AS (
    SELECT id, geometry_id, geometry
    FROM matching_relations_with_merged_polygons
    WHERE ref_role = 'inner'
)
SELECT
    op.id,
    op.geometry_id,
    ST_Difference(any_value(op.geometry), ST_Union_Agg(ip.geometry)) geometry
FROM outer_polygons op
JOIN inner_polygons ip
ON op.id = ip.id AND ST_WITHIN(ip.geometry, op.geometry)
GROUP BY op.id, op.geometry_id;

CREATE TEMP TABLE matching_relations_with_outer_polygons_without_holes AS
WITH outer_polygons AS (
    SELECT id, geometry_id, geometry
    FROM matching_relations_with_merged_polygons
    WHERE ref_role = 'outer'
)
SELECT
    op.id,
    op.geometry_id,
    op.geometry
FROM outer_polygons op
ANTI JOIN matching_relations_with_outer_polygons_with_holes opwh
ON op.id = opwh.id AND op.geometry_id = opwh.geometry_id;

SELECT * FROM matching_relations_with_outer_polygons_with_holes
UNION ALL
SELECT * FROM matching_relations_with_outer_polygons_without_holes;

┌──────────┬─────────────┬─────────────────────────────────────────────────────┐
│    id    │ geometry_id │                      geometry                       │
│  int64   │    int64    │                      geometry                       │
├──────────┼─────────────┼─────────────────────────────────────────────────────┤
│ 16248286 │           1 │ POLYGON ((7.4263139 43.7391602, 7.426324900000001…  │
│  1369192 │           1 │ POLYGON ((7.4226247 43.7402251, 7.4227848 43.7396…  │
│  8147763 │           1 │ POLYGON ((7.438223000000001 43.7477296, 7.4382403…  │
│   393226 │           1 │ POLYGON ((7.4204802 43.731016700000005, 7.4203979…  │
│ 11484092 │           4 │ POLYGON ((7.417896900000001 43.724827000000005, 7…  │
│ 11384697 │           1 │ POLYGON ((7.4274934 43.731250700000004, 7.4267196…  │
│  8269572 │           1 │ POLYGON ((7.4287166 43.7376724, 7.4287948 43.7376…  │
│ 16261416 │           3 │ POLYGON ((7.4178309 43.731391, 7.417877000000001 …  │
│ 16248284 │           1 │ POLYGON ((7.426637500000001 43.7394678, 7.4266485…  │
│ 16248285 │           1 │ POLYGON ((7.426114600000001 43.739267600000005, 7…  │
│     ·    │           · │                          ·                          │
│     ·    │           · │                          ·                          │
│     ·    │           · │                          ·                          │
│ 11546879 │           1 │ POLYGON ((7.4209316 43.7356234, 7.421037 43.73562…  │
│  2220206 │           1 │ POLYGON ((7.4120416 43.7280955, 7.412103900000001…  │
│  5986438 │           1 │ POLYGON ((7.4191482 43.738887500000004, 7.4199833…  │
│  2221178 │           1 │ POLYGON ((7.415860400000001 43.7313885, 7.416195 …  │
│  2221179 │           1 │ POLYGON ((7.422280000000001 43.7367186, 7.4227584…  │
│  2220208 │           1 │ POLYGON ((7.41849 43.730922400000004, 7.4185156 4…  │
│  2254506 │           1 │ POLYGON ((7.432995900000001 43.7447102, 7.4329125…  │
│  5986437 │           1 │ POLYGON ((7.4307593 43.745595, 7.4306621 43.74541…  │
│  5986437 │           2 │ POLYGON ((7.4343358 43.7457085, 7.4341337 43.7455…  │
│ 11546878 │           1 │ POLYGON ((7.4207413 43.7356673, 7.4207413 43.7357…  │
├──────────┴─────────────┴─────────────────────────────────────────────────────┤
│ 47 rows (20 shown)                                                 3 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

在这个查询中，使用了另一种特殊的连接——`ANTI JOIN`。这个连接会过滤掉所有左侧表中与右侧表连接的行。

最后一步是使用 `ST_Union_Agg` 操作将一个关系的所有多边形合并。它将把所有多边形合并为多多边形（如果有多个），并生成一个单一的几何体。

```py
WITH unioned_outer_geometries AS (
    SELECT id, geometry
    FROM matching_relations_with_outer_polygons_with_holes
    UNION ALL
    SELECT id, geometry
    FROM matching_relations_with_outer_polygons_without_holes
),
final_geometries AS (
    SELECT id, ST_Union_Agg(geometry) geometry
    FROM unioned_outer_geometries
    GROUP BY id
)
SELECT r_g.id, r.tags, r_g.geometry
FROM final_geometries r_g
JOIN matching_relations r
ON r.id = r_g.id;

┌──────────┬──────────────────────┬────────────────────────────────────────────┐
│    id    │         tags         │                  geometry                  │
│  int64   │ map(varchar, varch…  │                  geometry                  │
├──────────┼──────────────────────┼────────────────────────────────────────────┤
│   393226 │ {building=castle, …  │ POLYGON ((7.4204802 43.731016700000005, …  │
│   393481 │ {building=school, …  │ POLYGON ((7.423992800000001 43.731841800…  │
│  1369191 │ {building=apartmen…  │ POLYGON ((7.4231004 43.7412894, 7.423314…  │
│  1369192 │ {building=apartmen…  │ POLYGON ((7.4226247 43.7402251, 7.422784…  │
│  1369193 │ {building=apartmen…  │ POLYGON ((7.424 43.7405491, 7.4240401 43…  │
│  1369195 │ {building=apartmen…  │ POLYGON ((7.418679900000001 43.738187100…  │
│  1369631 │ {addr:housenumber=…  │ POLYGON ((7.4379729 43.7502505, 7.437937…  │
│  1369632 │ {addr:city=Monaco,…  │ POLYGON ((7.4317121 43.74709, 7.4318277 …  │
│  1484190 │ {addr:city=Monaco,…  │ POLYGON ((7.425469 43.731494000000005, 7…  │
│  1484217 │ {building=retail, …  │ POLYGON ((7.423202300000001 43.7307117, …  │
│     ·    │          ·           │                     ·                      │
│     ·    │          ·           │                     ·                      │
│     ·    │          ·           │                     ·                      │
│ 14399505 │ {area=yes, floatin…  │ POLYGON ((7.4195986 43.7265293, 7.419595…  │
│ 16248281 │ {building=apartmen…  │ POLYGON ((7.4260438 43.739789800000004, …  │
│ 16248282 │ {building=apartmen…  │ POLYGON ((7.426243100000001 43.7396824, …  │
│ 16248283 │ {building=apartmen…  │ POLYGON ((7.426438200000001 43.739575200…  │
│ 16248284 │ {building=apartmen…  │ POLYGON ((7.426637500000001 43.7394678, …  │
│ 16248285 │ {building=apartmen…  │ POLYGON ((7.426114600000001 43.739267600…  │
│ 16248286 │ {building=apartmen…  │ POLYGON ((7.4263139 43.7391602, 7.426324…  │
│ 16250182 │ {addr:country=MC, …  │ POLYGON ((7.418348300000001 43.7256979, …  │
│ 16261416 │ {natural=water, ty…  │ POLYGON ((7.4178309 43.731391, 7.4178770…  │
│ 11546878 │ {addr:city=Monaco,…  │ POLYGON ((7.4207413 43.7356673, 7.420741…  │
├──────────┴──────────────────────┴────────────────────────────────────────────┤
│ 46 rows (20 shown)                                                 3 columns │
└──────────────────────────────────────────────────────────────────────────────┘
```

这是之前的关系示例，现在带有孔洞：

![](../Images/217a6f093c325a233d93a5d152a284d4.png)

一个单一关系（[8280869](https://www.openstreetmap.org/relation/828086)）——一个带孔的多边形。由作者使用 GeoPandas 库生成。

![](../Images/206d0d2c26863920fd791636813f0d85.png)

绘制在地图上的关系几何体。由作者使用 GeoPandas 库生成。

# 错误定义的关系对象示例

由于 OpenStreetMap 数据主要由社区添加，因此有些几何体没有正确定义。OSM 维基描述了地图制作人员如何将几何体添加到地图中的规则。

[](https://wiki.openstreetmap.org/wiki/Relation:multipolygon/validity?source=post_page-----ffeb15197390--------------------------------) [## Relation:multipolygon/validity

### 本页面仅为提议，未代表关于什么有效或无效的共识，只是提出了可能的…

wiki.openstreetmap.org](https://wiki.openstreetmap.org/wiki/Relation:multipolygon/validity?source=post_page-----ffeb15197390--------------------------------)

本节将概述一些常见的错误，并附带示例。

## 关系中有两个重叠的“outer”路径

这座建筑由两个形状定义：一个矩形和一个几乎是圆形的形状。由于这两者重叠，可以看到渲染引擎在本不应该有空隙的地方产生了一个间隙。

![](../Images/671b8dd3fbcd20c09c06f10a11affd08.png)

作者提供的 OpenStreetMap 截图。

## 关系中没有“outer”路径

这里你可以看到一个带有 `way` 成员的彩弹场，成员被定义为“主建筑”和 4 个“竞技场”。这些都应该定义为 `outer` 路径。

![](../Images/927a31510d513e7214b9c5a477933ade.png)

作者提供的 OpenStreetMap 截图。

## 两个重叠或接触的“inner”路径

![](../Images/2dd6cb3b3ac6ea1ed1b73d63d44823ed.png)

作者提供的 OpenStreetMap 截图。

如果你有兴趣了解更多关于如何修复这些问题的信息，可以查阅这个代码库：

[](https://github.com/osmlab/fixing-polygons-in-osm/blob/master/doc/background.md?source=post_page-----ffeb15197390--------------------------------) [## fixing-polygons-in-osm/doc/background.md at master · osmlab/fixing-polygons-in-osm

### 修复 OpenStreetMap 中的（多重）多边形。通过创建账户，贡献于 osmlab/fixing-polygons-in-osm 的开发…

github.com](https://github.com/osmlab/fixing-polygons-in-osm/blob/master/doc/background.md?source=post_page-----ffeb15197390--------------------------------)

# QuackOSM — 一个便捷的工具，用于读取 OSM 数据

为了结束这篇文章，我想强调一个可以自动下载 OSM 数据的库，它可以通过几何或使用 OSM 标签过滤数据，并将其保存为 GeoParquet 文件，便于集成到更可扩展的解决方案中。这个库是用 Python 编写的，并且是开源的。

你可以通过一条命令安装它：`pip install quackosm[cli]`。

本教程包含了 QuackOSM 🦆 使用的查询的简化版本，但这些查询在更大区域中并不适用。该库可以轻松解析像法国这样的整个国家数据，即便在消费者级的 PC 上也能运行。如果需要，你当然可以使用 `SPATIAL` 扩展在处理后的 GeoParquet 文件上利用 DuckDB 引擎。

这里定义的所有步骤都可以通过一行代码来替代：

```py
>>> import quackosm as qosm
>>> qosm.get_features_gdf('monaco-latest.osm.pbf')
                                                              tags                                           geometry
feature_id                                                                                                           
way/834616137                               {'highway': 'footway'}  LINESTRING (7.42133 43.72711, 7.42134 43.72710...
way/408817108    {'addr:country': 'MC', 'building': 'yes', 'nam...  POLYGON ((7.43533 43.74965, 7.43534 43.74955, ...
way/686435440                               {'highway': 'footway'}  LINESTRING (7.41381 43.73258, 7.41388 43.73266...
node/7799514898              {'name': 'Cino Bar', 'shop': 'kiosk'}                           POINT (7.42702 43.73118)
way/143214794                                  {'building': 'yes'}  POLYGON ((7.42788 43.74437, 7.42786 43.74437, ...
...                                                            ...                                                ...
way/161882794    {'highway': 'secondary', 'lanes': '2', 'lit': ...  LINESTRING (7.42142 43.73656, 7.42147 43.73662...
way/1082330089             {'highway': 'steps', 'incline': 'down'}  LINESTRING (7.42438 43.72990, 7.42440 43.72988...
way/834313093    {'highway': 'service', 'smoothness': 'intermed...  LINESTRING (7.42709 43.73256, 7.42708 43.73262...
way/94399451             {'addr:country': 'MC', 'building': 'yes'}  POLYGON ((7.41678 43.73699, 7.41661 43.73689, ...
node/2462515787  {'crossing': 'uncontrolled', 'highway': 'cross...                           POINT (7.41656 43.73208)

[7940 rows x 2 columns]
>>> qosm.convert_pbf_to_gpq('monaco-latest.osm.pbf')
PosixPath('files/monaco-latest_nofilter_noclip_compact.geoparquet')
```

[](https://github.com/kraina-ai/quackosm?source=post_page-----ffeb15197390--------------------------------) [## GitHub - kraina-ai/quackosm: QuackOSM: an open-source Python and CLI tool for reading OpenStreetMap…

### QuackOSM：一个使用 DuckDB 读取 OpenStreetMap PBF 文件的开源 Python 和 CLI 工具 - kraina-ai/quackosm

github.com](https://github.com/kraina-ai/quackosm?source=post_page-----ffeb15197390--------------------------------)

## 免责声明

我是`QuackOSM`库的作者。

> 你可以通过这里联系我：
> 
> [https://www.linkedin.com/in/raczyckikamil/](https://www.linkedin.com/in/raczyckikamil/)
> 
> [https://github.com/raczeq](https://github.com/raczeq)
