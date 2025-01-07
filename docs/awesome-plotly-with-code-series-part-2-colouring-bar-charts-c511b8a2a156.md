# 用代码实现的精彩Plotly系列（第2部分）：条形图上色

> 原文：[https://towardsdatascience.com/awesome-plotly-with-code-series-part-2-colouring-bar-charts-c511b8a2a156?source=collection_archive---------3-----------------------#2024-10-26](https://towardsdatascience.com/awesome-plotly-with-code-series-part-2-colouring-bar-charts-c511b8a2a156?source=collection_archive---------3-----------------------#2024-10-26)

## 不要创建彩虹色的条形图。但也不要让你的条形图太单调无趣。

[](https://medium.com/@joparga3?source=post_page---byline--c511b8a2a156--------------------------------)[![Jose Parreño](../Images/707d5179926d36fba257f5476494e10e.png)](https://medium.com/@joparga3?source=post_page---byline--c511b8a2a156--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c511b8a2a156--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c511b8a2a156--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--c511b8a2a156--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c511b8a2a156--------------------------------) ·阅读时间：9分钟·2024年10月26日

--

![](../Images/2a6f3b7aec09c608801f10aa9e9b420e.png)

图像由Dall-e生成

*欢迎来到我“用代码实现Plotly”系列的第二篇文章！如果你错过了第一篇，可以通过下面的链接查看，或者通过我的“一个帖子统领所有”来跟随整个系列或我之前写过的其他主题。*

[](/awesome-plotly-with-code-series-part-1-alternatives-to-bar-charts-125502587690?source=post_page-----c511b8a2a156--------------------------------) [## 用代码实现的精彩Plotly系列（第1部分）：条形图的替代方案

### 条形图并不总是最佳的解决方案。

towardsdatascience.com](/awesome-plotly-with-code-series-part-1-alternatives-to-bar-charts-125502587690?source=post_page-----c511b8a2a156--------------------------------) [](https://medium.com/@joparga3/all-my-written-articles-in-one-place-24ccd6689f72?source=post_page-----c511b8a2a156--------------------------------) [## 我所有写过的文章都在这里

### 这是一个动态文档，请继续关注以获取更多更新！

medium.com](https://medium.com/@joparga3/all-my-written-articles-in-one-place-24ccd6689f72?source=post_page-----c511b8a2a156--------------------------------)

## 简短总结：我为什么要写这个系列

我常用的可视化工具是Plotly。它非常直观，从图层的添加到交互性的实现都非常便捷。然而，尽管Plotly在功能上表现出色，它并没有提供一种“数据新闻”模板，无法直接生成精美的图表。
