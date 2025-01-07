# 纽约市出租车数据可视化互动展示：Bokeh 与 Streamlit（第二部分）

> 原文：[`towardsdatascience.com/end-to-end-machine-learning-project-part-2-interactive-eda-using-bokeh-and-streamlit-24df51de4bee?source=collection_archive---------19-----------------------#2024-06-18`](https://towardsdatascience.com/end-to-end-machine-learning-project-part-2-interactive-eda-using-bokeh-and-streamlit-24df51de4bee?source=collection_archive---------19-----------------------#2024-06-18)

## 使用 Bokeh 和 Streamlit，通过动态可视化展示出租车行程模式，包括接送点热点和交通流量。

[](https://medium.com/@Pratha_P?source=post_page---byline--24df51de4bee--------------------------------)![Pratha Pawar](https://medium.com/@Pratha_P?source=post_page---byline--24df51de4bee--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--24df51de4bee--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24df51de4bee--------------------------------) [Pratha Pawar](https://medium.com/@Pratha_P?source=post_page---byline--24df51de4bee--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24df51de4bee--------------------------------) ·阅读时间 8 分钟·2024 年 6 月 18 日

--

![](img/f1f94ba17b4399d5e65cb9800100e632.png)

动画展示 2016 年 1 月到 6 月期间纽约市黄色出租车的上下车点。左图显示了 JFK 机场区域的接客点，右图显示了与左图中行程对应的下车点。动画从 0 点到 23 点运行，颜色表示该小时内的行程时长（以分钟为单位），并且是 6 个月的平均值。（图片来自作者）

查看本博客系列的第一部分 [启动你的机器学习之旅：数据范围、结构化与探索（第一部分）](https://medium.com/towards-data-science/end-to-end-machine-learning-project-part-1-project-scoping-structure-data-gathering-eda-7e69b71eeb44)，以理解我们要解决的数据和业务问题。

**本文涉及的内容：**

+   向纽约市出租车行程数据集添加地理地图功能

+   使用 Bokeh 创建互动式图表

+   将 bokeh 图表部署到 streamlit ([`nyc-taxi-trip-pp.streamlit.app/`](https://nyc-taxi-trip-pp.streamlit.app/)) — 注意：请使用暗色主题

*Github 链接:* [*本文源代码*](https://github.com/pratha19/NYC_taxi_trip/tree/pp_nyc_trip_blog_1_2_pip)

本文将重点讨论业务问题中的以下两个主题：

1.  **识别不同时段、不同区域的纽约市人群（在出租车中的人群）流动情况。**

1.  **识别不同时间和不同区域的出租车需求（哪些是热点区域？）**

作为其中的一部分，我们将创建一个互动式可视化，提供两个选项，展示：

1.  **散点图**：每次旅行…
