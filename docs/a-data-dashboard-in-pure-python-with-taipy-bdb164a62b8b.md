# 使用 Taipy 在纯 Python 中创建数据仪表盘

> 原文：[https://towardsdatascience.com/a-data-dashboard-in-pure-python-with-taipy-bdb164a62b8b?source=collection_archive---------2-----------------------#2024-06-25](https://towardsdatascience.com/a-data-dashboard-in-pure-python-with-taipy-bdb164a62b8b?source=collection_archive---------2-----------------------#2024-06-25)

## 数据可视化

## Taipy 是一个创新框架，旨在简化交互式和视觉吸引力强的数据科学应用程序的创建。

[](https://medium.com/@alan-jones?source=post_page---byline--bdb164a62b8b--------------------------------)[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--bdb164a62b8b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdb164a62b8b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bdb164a62b8b--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--bdb164a62b8b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdb164a62b8b--------------------------------) ·13 分钟阅读·2024年6月25日

--

![](../Images/44424dcd6eaa742a3af20de0604ea6b1.png)

我笔记本上的 Taipy 仪表盘

Taipy 框架面向数据科学和数据可视化社区，因此与 Streamlit 处于同一领域竞争。尽管如此，正如我们将看到的，它是一个完全不同的工具。

在本教程中，我们将介绍 Taipy 的基本概念，并指导您创建一个 CO2 排放可视化应用程序。这个示例将展示如何使用 Taipy 构建一个交互式应用程序，通过分级色块地图及配套的图表和表格来可视化全球 CO2 排放数据。

最终的应用程序将呈现如下截图所示的样式。

![](../Images/28814632153cae48567cfaaba0a9f9c2.png)

仪表盘截图

如您所见，屏幕顶部有一个横幅标题和两个图表：左侧是分级色块地图，右侧是柱状图。分级色块地图显示了每个国家在某一年份的总 CO2 排放量，而柱状图则显示了所选国家的 CO2 排放量…
