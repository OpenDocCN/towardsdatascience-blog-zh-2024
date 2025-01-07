# 使用 Google Mesop 进行数据可视化实战

> 原文：[`towardsdatascience.com/hands-on-data-visualization-with-google-mesop-bdde37ef09da?source=collection_archive---------3-----------------------#2024-10-25`](https://towardsdatascience.com/hands-on-data-visualization-with-google-mesop-bdde37ef09da?source=collection_archive---------3-----------------------#2024-10-25)

## Google Mesop 是一个易于使用的 Python UI 框架。我们将展示如何利用它与 Plotly 一起创建数据可视化应用。

[](https://medium.com/@alan-jones?source=post_page---byline--bdde37ef09da--------------------------------)![Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--bdde37ef09da--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdde37ef09da--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdde37ef09da--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--bdde37ef09da--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdde37ef09da--------------------------------) ·阅读时间 14 分钟·2024 年 10 月 25 日

--

![](img/35a7dcaa2a796ad221be57015f05f045.png)

通过将 Mesop 简单而强大的 UI 组件与 Plotly 强大的图表功能相结合，你可以构建一个动态、互动的应用，展示 Python 中的数据可视化效果。

Mesop 是一个原生 Python 框架，旨在快速开发 AI 和 Web 应用，它允许你在无需传统前端技能的情况下构建复杂的界面；Plotly，当然，是一个广为人知且被广泛使用的图形库。

我们将使用这些组件来构建一个数据可视化应用。

# Mesop

Mesop 不是 Google 官方支持的产品，但它文档齐全，在 Google 内部广泛使用，并且正受到相当的关注，因此我认为我们可以认真对待它。Mesop 是一个开源项目，使用 [Apache-2.0 许可协议](https://github.com/google/mesop#Apache-2.0-1-ov-file) [1]。

这是一个在 Mesop 中实现的“Hello World”应用。

```py
import mesop as me

@me.page(path="/")
def app():
  me.text("Hello World")
```

*Mesop 的 Hello World*
