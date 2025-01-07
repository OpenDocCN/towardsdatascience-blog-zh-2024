# 从数据到仪表盘：使用 Dash Leaflet 和 SeaRoute 库可视化古代海上丝绸之路

> 原文：[`towardsdatascience.com/from-data-to-dashboard-visualizing-the-ancient-maritime-silk-road-with-dash-leaflet-and-searoute-ac8a521ac4e9?source=collection_archive---------5-----------------------#2024-05-22`](https://towardsdatascience.com/from-data-to-dashboard-visualizing-the-ancient-maritime-silk-road-with-dash-leaflet-and-searoute-ac8a521ac4e9?source=collection_archive---------5-----------------------#2024-05-22)

## 在 Python Dash 框架中构建一个交互式地图可视化，展示海上两点之间的最短路径

[](https://medium.com/@m.mouschoutzi?source=post_page---byline--ac8a521ac4e9--------------------------------)![Maria Mouschoutzi, 博士](https://medium.com/@m.mouschoutzi?source=post_page---byline--ac8a521ac4e9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ac8a521ac4e9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ac8a521ac4e9--------------------------------) [Maria Mouschoutzi, 博士](https://medium.com/@m.mouschoutzi?source=post_page---byline--ac8a521ac4e9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ac8a521ac4e9--------------------------------) ·14 分钟阅读·2024 年 5 月 22 日

--

![](img/e430711b8b2d802a344d46754c39f7f1.png)

图片来自 [Annie Spratt](https://unsplash.com/@anniespratt?utm_source=medium&utm_medium=referral) 提供的 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我博士学位期间的一个难点是创建一个交互式地图可视化，展示船只从一个港口到另一个港口的航运路线。船只从起点到目的地港口的航程应该完全在海上，不可穿越任何陆地。令人惊讶的是，这个看似简单的任务在尝试用 Python 从零开始实现时竟然非常具有挑战性 🤷‍♀️。虽然有像 Marine Traffic 这样的商业解决方案可以完成类似的任务，但我一直在寻找开源的替代方案，并且很长一段时间内都没找到。直到 2022 年底，SeaRoute 库发布了 Python 版本（之前仅支持 Java），它极大地简化了我的工作。在这篇文章中，我将带你通过创建 Dash 应用程序的交互式地图可视化的过程，利用 Dash Leaflet 和 SeaRoute Python 库展示海上航线。

# 那么 Dash、Dash Leaflet 和 SeaRoute 呢？
