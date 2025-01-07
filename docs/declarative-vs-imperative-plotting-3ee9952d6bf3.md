# 声明式与命令式绘图

> 原文：[https://towardsdatascience.com/declarative-vs-imperative-plotting-3ee9952d6bf3?source=collection_archive---------9-----------------------#2024-01-10](https://towardsdatascience.com/declarative-vs-imperative-plotting-3ee9952d6bf3?source=collection_archive---------9-----------------------#2024-01-10)

## 快速成功的数据科学

## 为 Python 初学者提供概览

[](https://medium.com/@lee_vaughan?source=post_page---byline--3ee9952d6bf3--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--3ee9952d6bf3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3ee9952d6bf3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3ee9952d6bf3--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--3ee9952d6bf3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3ee9952d6bf3--------------------------------) ·阅读时间 11 分钟 ·2024年1月10日

--

![](../Images/ad785aa5605ee5542cbfea4535622f7e.png)

声明式与命令式由列奥纳多的绝对现实 v16 进行想象

如果你正在学习 Python，预计你会用[Matplotlib](https://matplotlib.org/)制作你的第一个图表。Matplotlib 不仅极其流行，而且是一个*命令式*绘图库。这意味着它使用逐步的方式生成图形，这对初学者来说很容易理解。

Python 也支持*声明式*绘图库，例如[seaborn](https://seaborn.pydata.org/)、[Altair](https://altair-viz.github.io/)和[HoloViews](https://holoviews.org/Reference_Manual/index.html)，它们让你专注于*图表应展示什么内容*，而不是*如何绘制它*。引用 Altair 文档中的话，“关键思想是你在声明*数据列*与*视觉编码通道*之间的*联系*，例如 x 轴、y 轴和颜色。其余的绘图细节会自动处理。基于这个声明式系统，可以通过简洁的语法创建出从简单到复杂的各种图表。”

科学家和工程师应该会觉得声明式方法很有吸引力，因为它能让他们把更多时间花在实际工作上，而不是编写代码。正如我喜欢说的，“科学优先，编码其次！”

在这篇*快速成功的数据科学*文章中，我们将探讨命令式和声明式绘图。主要的重点将放在声明式风格上，使用来自 seaborn、[Plotly Express](https://plotly.com/python/plotly-express/)和[hvplot](https://hvplot.holoviz.org/)的示例。
