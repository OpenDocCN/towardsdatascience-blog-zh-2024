# 精彩的Plotly与代码系列（第一部分）：条形图的替代方案

> 原文：[https://towardsdatascience.com/awesome-plotly-with-code-series-part-1-alternatives-to-bar-charts-125502587690?source=collection_archive---------2-----------------------#2024-10-21](https://towardsdatascience.com/awesome-plotly-with-code-series-part-1-alternatives-to-bar-charts-125502587690?source=collection_archive---------2-----------------------#2024-10-21)

## 条形图并不总是最佳的解决方案。

[](https://medium.com/@joparga3?source=post_page---byline--125502587690--------------------------------)[![Jose Parreño](../Images/707d5179926d36fba257f5476494e10e.png)](https://medium.com/@joparga3?source=post_page---byline--125502587690--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--125502587690--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--125502587690--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--125502587690--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--125502587690--------------------------------) ·阅读时间13分钟·2024年10月21日

--

![](../Images/5c654088031e684b3efe2dc258ea67dd.png)

图片由Dall-E生成

# 系列简介

数据可视化在讲述和理解故事中发挥着至关重要的作用。我一直对数据新闻中使用的流畅、带注解的图表感到着迷——这些可视化图表能够瞬间传达复杂的想法，让任何人都能理解。

我也深受[像Cole Nussbaumer Knaflic的书《*数据讲故事*》](https://medium.com/@joparga3/book-summary-storytelling-with-data-by-cole-nussbaumer-f38d0f588c4d)[1]的启发，该书提供了创建清晰、富有影响力的可视化的基本最佳实践，强调了一种极简主义的方法，去除不必要的元素。她的方法是专注于真正重要的内容，确保数据的故事不被干扰，充分展现其核心。

另外，还有AddTwoDigital，这是一家专注于数据故事讲述的数字机构。[他们已经开源了一系列关于数据可视化最佳实践的博客文章](https://www.addtwodigital.com/resources) [2]，展示了从简单的条形图到更复杂、令人费解的信息图的各种内容。他们的内容是灵感的宝藏，从他们的示例中总能学到一些新东西，而这些东西通常不会出现在典型的数据可视化书籍中。
