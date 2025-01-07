# 在 R 中使用 Google Earth 绘制高尔夫球场

> 原文：[https://towardsdatascience.com/plotting-golf-courses-in-r-with-google-earth-8ee8aa6f6293?source=collection_archive---------5-----------------------#2024-05-06](https://towardsdatascience.com/plotting-golf-courses-in-r-with-google-earth-8ee8aa6f6293?source=collection_archive---------5-----------------------#2024-05-06)

## 一份关于如何在 Google Earth 中绘制高尔夫球场并将其在 R 中呈现的用户指南。

[](https://medium.com/@adam.c.beaudet?source=post_page---byline--8ee8aa6f6293--------------------------------)[![Adam Beaudet](../Images/f5084b418f043e11f35a50a6a63d507f.png)](https://medium.com/@adam.c.beaudet?source=post_page---byline--8ee8aa6f6293--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8ee8aa6f6293--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8ee8aa6f6293--------------------------------) [Adam Beaudet](https://medium.com/@adam.c.beaudet?source=post_page---byline--8ee8aa6f6293--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8ee8aa6f6293--------------------------------) ·阅读时间 7 分钟·2024年5月6日

--

在数据可视化的世界里，我们常常被条形图、折线图和饼图淹没。但事实并不一定非得如此——在本文中，我将展示如何绘制高尔夫球场。

![](../Images/64d7018ea587cf6c48482bb7237bb72b.png)

图片由作者提供

# **介绍**

全球大约有 40,000 个高尔夫球场。如果你像我一样热衷于高尔夫和数据，那么这篇文章就是为你准备的。在绘制一个球场后，我们还可以做一些很酷的事情：

+   **将击球数据叠加到球场地图上——** 这可以通过使用 Plotly 包进行一些操作，或者通过从 Google Earth 下载单独的高尔夫球击打“位置标记”并将其作为点绘制到地图上。对于你第一次打破90杆、80杆、70杆等的时刻，这可能是一个不错的纪念方式。

+   **计算球场指标**——一般来说，大家都知道 Pebble Beach 的果岭很小，被称为“小邮票果岭”。或者 Whistling Straits 有很多沙坑。通过描绘球场元素的多边形，另一个附带的好处是可以计算每个元素的面积。这使我们能够为我们绘制的任何球场推导出平均果岭大小、沙坑数量、球道平均宽度等信息。

上述要点并不是一个全面的列表，但它为项目提供了一个未来扩展的路线图。

# **目录**

我将首先介绍项目的步骤，然后详细讲解每一步：

1.  在 Google Earth 中描绘高尔夫球场的多边形，代表高尔夫球场的各个元素（发球台、球道、沙坑、果岭、水域、障碍物）

1.  从 Google Earth 下载多边形为 KML 文件

1.  读取 KML 数据到 R 中并进行一些轻度的数据清洗/操作

1.  使用 ggplot2 绘制高尔夫球场

# **在 Google Earth 中绘制多边形**

首先，让我们前往 Google Earth，选择一个我们想要绘制的高尔夫球场。我们将以位于威斯康星州的 Erin Hills 为例。通常，熟悉球场布局或提前打开一张球场地图有助于通过卫星影像更容易地识别每个洞的具体位置。

我们需要通过点击左上角的蓝色“+ 新建”按钮来创建一个新项目。接下来，我们将使用本地的 KML 文件并点击“创建”。最后，为项目命名，通常可以直接使用所绘制课程的名称。项目的名称将是我们完成后下载的 KML 文件的名称。

![](../Images/0f0199f76494e9b19a4662505077abf1.png)

来自 Google Earth 的图片，由作者编辑

现在我们的项目文件夹已经设置好，可以开始绘制了。**免责声明**：Erin Hills 有 138 个沙坑，我是通过亲身体验发现绘制它们有些繁琐……不过，还是让我们先前往第一个发球台开始绘制吧。

到达第一个发球台后，首先识别该洞的关键元素。Erin Hills 的第一个洞有水障碍和一个位于果岭左侧的危险区，果岭有一个左转的狗腿，几处沙坑等等。要开始绘制，请点击“添加路径或多边形”，这是位于顶部工具栏中第二个从左边开始的图标，图标形状为一条连接的点线。这将初始化一个类似铅笔的工具，我们可以用它来进行绘制。

**附加说明**：你可以通过同时按住 Shift 键并按左箭头或右箭头来旋转屏幕。

我通常从发球台开始，然后向果岭方向绘制。要求每个绘制的多边形都必须形成一个闭合的形状，这意味着你需要回到原始的起点。完成一个多边形后，将其保存到项目中并为其命名。为每个多边形命名时，使用一致的命名规范也非常重要，例如**course_hole_element**，在这种情况下可以翻译为：**erin_hills_hole_1_tee**，或者**erin_hills_hole_5_fairway**，等等。我们稍后会在 R 代码中使用字符串匹配来提取每个多边形名称中的这些关键信息。这将帮助我们创建一个多边形元素到颜色的映射，也就是告诉 ggplot2 如何为每个多边形上色。因此，如果“沙坑”是元素，那么我们希望将其着色为棕黄色。如果“水障碍”是元素，它应该是蓝色的。这样也可以帮助我们提取课程名称和洞号，从而提供更多的绘图功能。

下图是 Erin Hills 的第 15 洞（我在这里打球时最喜欢的洞）。左侧是原始的 Google Earth 图像，中间是我们绘制过后的图像，右侧是使用 ggplot2 渲染后的图像。我选择不绘制球场的长草区、树木、球车道等元素。

![](../Images/a6c3a72c26da37453e5cfbf44c9bd968.png)![](../Images/a5f8a89ee3d185e97d27035f465c1423.png)![](../Images/89e91b1153ebf02adcb95b20b2caa32c.png)

(**左**) 来自 Google Earth 的照片，(**中**) 来自 Google Earth 的照片，由作者编辑，(**右**) 作者提供的图片

一旦我们完成了球洞或球场的绘制，就该把所有辛苦工作的成果导出为KML文件了。可以通过点击屏幕左侧项目所在位置的三个竖点来完成此操作。该项目与geoJSON数据最为兼容，我们可以在接下来的步骤中轻松地将KML文件转换为geoJSON格式。现在我们准备好进入R了。

# **在R中绘图**

我们需要准备的包有：**sf**（用于处理地理空间数据）、**tidyverse**（用于数据清理和绘图）、**stringr**（用于字符串匹配）和**geojsonsf**（用于将KML转换为geoJSON）。我们的第一步是读取KML文件，这可以通过**st_read()**函数来实现。

```py
# load libraries
 library(sf)
 library(tidyverse)
 library(stringr)
 library(geojsonsf)

kml_df <- st_read("/Users/adambeaudet/Downloads/erin_hills.kml")
```

太好了！现在我们应该已经在R中获取了高尔夫球场的KML数据。数据框应该有两列：**Name**（项目名称，或者在我们这个案例中是球场名称），和**geometry**（一个包含所有构成我们描绘的多边形的单个点的列表）。如前所述，让我们将KML数据转换为geoJSON，并提取球场名称和洞号。

```py
# convert from KML to geoJSON
geojson_df <- st_as_sf(kml_df, "POLYGON")

# extracting course name and hole number from polygon name
# assuming "course_hole_element" naming convention is used for polygons
geojson_df$course_name <- str_match(geojson_df$Name, “^(.+)_hole”)[,2] 
geojson_df$hole_num <- gsub(“.*_hole_(\\d+)_.*”, “\\1”, geojson_df$Name)
```

为了使我们的地图指向正北方，我们需要以一种保持方向性的方式进行投影。我们可以使用**st_transform()**函数来做到这一点。

```py
# define a CRS so map always points due north
crs <- "+proj=lcc +lat_1=33 +lat_2=45 +lat_0=39 +lon_0=-96 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"

# transform data to CRS
geojson_df <- st_transform(geojson_df, crs)
```

我们几乎准备好绘制了，但首先，我们需要告诉ggplot2如何为每个多边形上色。下面是我的项目使用的调色板，您也可以根据需要自定义。

**可选**：在此步骤中，我们还可以使用**st_centroid()**函数计算我们的多边形的重心，这样我们就可以将洞号叠加到每个果岭上。

![](../Images/577b95d109ebd41ce97372c44f0a77d7.png)

图片由作者提供

```py
geojson_df <- geojson_df %>%
 mutate(color = case_when(
 grepl(“_tee$”, Name) ~ “#57B740”,
 grepl(“_bunker$”, Name) ~ “#EDE6D3”,
 grepl(“_water$”, Name) ~ “#2243b6”,
 grepl(“_fairway$”, Name) ~ “#57B740”,
 grepl(“_green$”, Name) ~ “#86D14A”,
 grepl(“_hazard$”, Name) ~ “#094d1d”
 )) %>%
mutate(centroid = st_centroid(geometry))
```

我们正式准备好绘图了。我们可以结合使用**geom_sf()**、**geom_text()**，如果想要更花哨一点，还可以使用**geom_point()**来绘制地图上的击球位置。我通常会去掉网格线、坐标轴标签和图例，以保持界面的简洁。

```py
ggplot() +
geom_sf(data = geojson_df, aes(fill = color), color = "black") +
geom_text(data = filter(geojson_df, grepl("_green$", Name)),
aes(x = st_coordinates(centroid)[, 1],
y = st_coordinates(centroid)[, 2],
label = hole_num),
size = 3, color = "black", fontface = "bold", hjust = 0.5, vjust = 0.5) +
scale_fill_identity() +
theme_minimal() +
theme(axis.title.x = element_blank(),
axis.title.y = element_blank(),
axis.text.x = element_blank(),
axis.text.y = element_blank(),
plot.title = element_text(size = 16),
panel.grid.major = element_blank(),
panel.grid.minor = element_blank()) +
theme(legend.position = "none") +
labs(title = 'Erin Hills | Hartford, WI')
```

就这样——在R中绘制的高尔夫球场，真是个好主意！

要查看我在撰写本文时所绘制的其他课程，您可以访问我的Shiny应用：[https://abodesy14.shinyapps.io/golfMapsR/](https://abodesy14.shinyapps.io/golfMapsR/)

如果您跟随本教程并且玩得开心，或者感兴趣的话，欢迎尝试绘制您最喜欢的高尔夫球场，并为我维护的**golfMapsR**仓库创建一个Pull Request：[https://github.com/abodesy14/golfMapsR](https://github.com/abodesy14/golfMapsR)

通过一些共同的努力，我们可以创建一个关于全球高尔夫球场的可绘制数据库！
