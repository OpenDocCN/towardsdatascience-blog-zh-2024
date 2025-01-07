# 四个与Pandas数据框无缝集成的可视化库

> 原文：[https://towardsdatascience.com/four-visualisation-libraries-that-seamlessly-integrate-with-pandas-dataframe-b0dd851966c2?source=collection_archive---------6-----------------------#2024-08-13](https://towardsdatascience.com/four-visualisation-libraries-that-seamlessly-integrate-with-pandas-dataframe-b0dd851966c2?source=collection_archive---------6-----------------------#2024-08-13)

![](../Images/08d15669f3628a0259599f1db62a084b.png)

图片由作者在Canva中创建

## 利用Pandas绘图后端实现最简便的绘图

[](https://christophertao.medium.com/?source=post_page---byline--b0dd851966c2--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--b0dd851966c2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b0dd851966c2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b0dd851966c2--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--b0dd851966c2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b0dd851966c2--------------------------------) ·阅读时间：5分钟·2024年8月13日

--

# 介绍

几周前，我写了一篇文章，讲述了如何使用Pandas直接绘制其数据框，而无需导入任何数据可视化库。实际上，它默认使用Matplotlib作为“绘图后端”。

[](/you-dont-need-matplotlib-when-pandas-is-enough-for-data-visualisation-38d5680197a5?source=post_page-----b0dd851966c2--------------------------------) [## 当Pandas足够用于数据可视化时，你不需要Matplotlib

### 一行代码绘制数据，使日常的EDA工作更轻松

[https://towardsdatascience.com/you-dont-need-matplotlib-when-pandas-is-enough-for-data-visualisation-38d5680197a5?source=post_page-----b0dd851966c2--------------------------------](https://towardsdatascience.com/you-dont-need-matplotlib-when-pandas-is-enough-for-data-visualisation-38d5680197a5?source=post_page-----b0dd851966c2--------------------------------)

实际上，Pandas绘图后端就像一个API，其他库可以实现这个API。因此，除了Matplotlib，还有许多其他出色的库可以作为其后端。这意味着它们都支持在我们直接绘制Pandas数据框时作为可视化工具。

在本文中，我将介绍四个实现Pandas数据框后端API的库。因此，它们可以直接用于从数据框中绘图，甚至无需导入。

# 0. 准备工作

如前所述，一些Python数据可视化库可以与Pandas更好地集成……
