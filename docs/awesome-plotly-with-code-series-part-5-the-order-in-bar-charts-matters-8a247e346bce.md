# 用代码做 Plotly 系列（第五部分）：条形图的排序很重要

> 原文：[`towardsdatascience.com/awesome-plotly-with-code-series-part-5-the-order-in-bar-charts-matters-8a247e346bce?source=collection_archive---------7-----------------------#2024-12-10`](https://towardsdatascience.com/awesome-plotly-with-code-series-part-5-the-order-in-bar-charts-matters-8a247e346bce?source=collection_archive---------7-----------------------#2024-12-10)

## 而且它并不总是简单地按从高到低排序

[](https://medium.com/@joparga3?source=post_page---byline--8a247e346bce--------------------------------)![Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--8a247e346bce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8a247e346bce--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8a247e346bce--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--8a247e346bce--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8a247e346bce--------------------------------) ·阅读时间 10 分钟·2024 年 12 月 10 日

--

![](img/2dd7a338bac42646a6fccb57a2b43a5d.png)

图片由 Dall-e 生成

*欢迎来到我“用代码做 Plotly”系列的第五篇文章！如果你错过了第一篇，可以通过下面的链接查看，或者浏览我的“统领所有文章”系列，跟随我一起了解整个系列或者我之前写过的其他话题。*

[](/awesome-plotly-with-code-series-part-1-alternatives-to-bar-charts-125502587690?source=post_page-----8a247e346bce--------------------------------) ## 用代码做 Plotly 系列（第一部分）：条形图的替代方案

### 条形图并不总是最好的解决方案。

towardsdatascience.com [](https://medium.com/@joparga3/all-my-written-articles-in-one-place-24ccd6689f72?source=post_page-----8a247e346bce--------------------------------) [## 我的所有文章集中展示

### 这是一个实时文档，敬请期待更多更新！

medium.com](https://medium.com/@joparga3/all-my-written-articles-in-one-place-24ccd6689f72?source=post_page-----8a247e346bce--------------------------------)

## 我写这个系列的简短总结

我常用的可视化工具是 Plotly。它非常直观，从叠加轨迹到添加互动性都十分容易。然而，尽管 Plotly 在功能上表现出色，但它并没有一个“数据新闻”模板，可以直接提供打磨过的图表。
