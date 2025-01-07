# Awesome Plotly与代码系列（第三部分）：在长尾中突出显示条形图

> 原文：[https://towardsdatascience.com/awesome-plotly-with-code-series-part-3-highlighting-bars-in-the-long-tails-8e5116e36003?source=collection_archive---------6-----------------------#2024-11-01](https://towardsdatascience.com/awesome-plotly-with-code-series-part-3-highlighting-bars-in-the-long-tails-8e5116e36003?source=collection_archive---------6-----------------------#2024-11-01)

## 谁说长尾不重要？让我们为它们提供一个突出显示的合适方式

[](https://medium.com/@joparga3?source=post_page---byline--8e5116e36003--------------------------------)[![Jose Parreño](../Images/707d5179926d36fba257f5476494e10e.png)](https://medium.com/@joparga3?source=post_page---byline--8e5116e36003--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8e5116e36003--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8e5116e36003--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--8e5116e36003--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8e5116e36003--------------------------------) ·阅读时间：7分钟·2024年11月1日

--

![](../Images/ea226b2591b5126fdee7094288d79df9.png)

图像由Dall-e生成

*欢迎来到我的“Plotly与代码”系列的第三篇文章！如果你错过了第一篇，你可以通过下面的链接查看，或者浏览我的“单篇文章大全”来跟随整个系列或其他我之前写过的主题。*

[](/awesome-plotly-with-code-series-part-1-alternatives-to-bar-charts-125502587690?source=post_page-----8e5116e36003--------------------------------) [## Awesome Plotly与代码系列（第一部分）：条形图的替代方案

### 条形图并不总是最好的解决方案。

[所有我的写作文章都在这里](https://medium.com/@joparga3/all-my-written-articles-in-one-place-24ccd6689f72?source=post_page-----8e5116e36003--------------------------------) 

### 这是一个实时更新的文档，请持续关注更多新增内容！

[medium.com](https://medium.com/@joparga3/all-my-written-articles-in-one-place-24ccd6689f72?source=post_page-----8e5116e36003--------------------------------)

## 简短总结我为何要写这一系列文章

我的首选工具是Plotly来创建可视化。它非常直观，从图层的叠加到添加互动性。然而，虽然Plotly在功能上表现出色，但它并没有提供一个“数据新闻”模板，来提供精美的图表…
