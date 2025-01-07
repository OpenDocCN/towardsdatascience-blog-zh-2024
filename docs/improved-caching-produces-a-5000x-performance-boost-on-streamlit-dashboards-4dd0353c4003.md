# 改进的缓存技术使 Streamlit 仪表板性能提升了 5000 倍

> 原文：[`towardsdatascience.com/improved-caching-produces-a-5000x-performance-boost-on-streamlit-dashboards-4dd0353c4003?source=collection_archive---------4-----------------------#2024-01-15`](https://towardsdatascience.com/improved-caching-produces-a-5000x-performance-boost-on-streamlit-dashboards-4dd0353c4003?source=collection_archive---------4-----------------------#2024-01-15)

## PYTHON 编程

## 了解如何使用原生 Python 缓存来实现极速的仪表板。

[](https://medium.com/@nyggus?source=post_page---byline--4dd0353c4003--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--4dd0353c4003--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4dd0353c4003--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4dd0353c4003--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--4dd0353c4003--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4dd0353c4003--------------------------------) ·10 分钟阅读·2024 年 1 月 15 日

--

![](img/53e7a736b2a449b22b98cb9995fa5d8a.png)

图片由 [Myriam Jessier](https://unsplash.com/@mjessier?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我几个月前开始使用它，必须说我真的很喜欢。之前我使用过 [R Shiny](https://shiny.posit.co/) 和 Python 的 [dash](https://pypi.org/project/dash/) 仪表板，但在编程简易性方面，[Streamlit](https://streamlit.io/) 比它们都要强。我发现它非常易于使用。虽然我从来不特别喜欢实现仪表板（我也不确定为什么），但 Streamlit 实际上改变了这一点；它变得很有趣！

然而，Streamlit 也有一些缺点。一个是 Streamlit 的代码往往看起来像是糟糕的 Python 代码，因为仪表板通常没有使用函数实现，而只是简单地列出一行行的代码。一些功能可以被移到函数或类中，但有时最好不要这么做。

Streamlit 的另一个缺点是其仪表板可能会很慢，原因有几个。首先，每当用户与仪表板进行交互时，Streamlit 都会重新运行整个仪表板脚本，这可能意味着需要重新运行例如计算、数据读取、图形和图表等操作。

在本文中，我们将讨论如何通过缓存加速 Streamlit 数据。我不会告诉你...
