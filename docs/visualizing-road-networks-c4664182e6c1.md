# 可视化道路网络

> 原文：[https://towardsdatascience.com/visualizing-road-networks-c4664182e6c1?source=collection_archive---------6-----------------------#2024-07-26](https://towardsdatascience.com/visualizing-road-networks-c4664182e6c1?source=collection_archive---------6-----------------------#2024-07-26)

![](../Images/be9d8e4dc50c46b9eecc7dfc1deac5d9.png)

## 如何使用 Python 和 OSMnx 创建全球城市道路网络的美丽可视化。

[](https://medium.com/@janosovm?source=post_page---byline--c4664182e6c1--------------------------------)[![Milan Janosov](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--c4664182e6c1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c4664182e6c1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c4664182e6c1--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--c4664182e6c1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4664182e6c1--------------------------------) ·7分钟阅读·2024年7月26日

--

道路网络是城市的美丽鸟瞰图。然而，它们的重要性远远超出作为壁纸的眼球糖果。事实上，对于训练有素的城市规划师来说，城市道路网络的视觉结构已经暗示了许多关于城市规划（如果有的话）以及城市可能采取的发展路径的信息，以及当前或不久的将来可能影响城市的潜在陷阱和问题。这些问题可以从公共和私人交通规划到绿色基础设施的改善，再到服务和设施的可达性。

在这篇文章中，我通过使用[OSMnx](https://osmnx.readthedocs.io/en/stable/)库下载全球多个城市的道路网络，向你介绍这个过程的第一步。

*所有图像均由作者创建。*

# 原型设计道路网络可视化过程

让我们从一些简单且快速处理的内容开始，比如一个较小的城市区域。例如，我使用布达佩斯的市中心，第五区。

首先，使用 OSMnx，我下载城市的行政边界，然后使用*graph_from_polygon*命令…
