# Streamlit 支持 5 个重要的数据可视化库 — 该选择哪个？

> 原文：[https://towardsdatascience.com/streamlit-supports-5-important-data-visualization-libraries-which-to-choose-8f0c6d19c705?source=collection_archive---------5-----------------------#2024-05-16](https://towardsdatascience.com/streamlit-supports-5-important-data-visualization-libraries-which-to-choose-8f0c6d19c705?source=collection_archive---------5-----------------------#2024-05-16)

## 我们将使用 Altair、Bokeh、Plotly、Pandas Plot 和 Matplotlib 编写示例代码，以说明每个库的优缺点。

[](https://medium.com/@alan-jones?source=post_page---byline--8f0c6d19c705--------------------------------)[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--8f0c6d19c705--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8f0c6d19c705--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8f0c6d19c705--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--8f0c6d19c705--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8f0c6d19c705--------------------------------) ·17分钟阅读·2024年5月16日

--

![](../Images/2b5d201f2a28dde0c5f07fd1c01d0c63.png)

图片由作者借助 Microsoft Image Creator 创建

想象一下，你的组织已经决定将 Streamlit 作为展示数据可视化应用的主要平台，并且为了确保一致的外观，它希望采用一个图表库，供所有应用使用。假设你的工作是调查哪个库最合适。

你有很多选择！你可以使用 5 个库来编码你的数据可视化：Altair、Bokeh、Plotly、Pyplot（Matplotlib）和 Vega Lite。而 Streamlit 也提供了一些原生图表。

让我们逐一看看这些库，并编写一些常用的图表。

我们将使用的一组数据是两种加密货币的价格数据，从这些数据中，我们将创建一张折线图，显示两种货币在一年内收盘价的变化；一张分组条形图，显示相同的数据，同样适用于两种货币；以及一张带有趋势线的散点图，展示这两种货币价值变化之间的相关性。
