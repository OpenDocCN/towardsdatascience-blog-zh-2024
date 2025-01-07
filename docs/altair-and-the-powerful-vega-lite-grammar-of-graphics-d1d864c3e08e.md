# 精通数据可视化与 Altair 的图形语法

> 原文：[`towardsdatascience.com/altair-and-the-powerful-vega-lite-grammar-of-graphics-d1d864c3e08e?source=collection_archive---------7-----------------------#2024-02-27`](https://towardsdatascience.com/altair-and-the-powerful-vega-lite-grammar-of-graphics-d1d864c3e08e?source=collection_archive---------7-----------------------#2024-02-27)

## 数据可视化

## 使用 Altair 强大的 Python 库将数据转化为可视化图形。

[](https://medium.com/@alan-jones?source=post_page---byline--d1d864c3e08e--------------------------------)![Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--d1d864c3e08e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d1d864c3e08e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d1d864c3e08e--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--d1d864c3e08e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d1d864c3e08e--------------------------------) ·阅读时间：20 分钟·2024 年 2 月 27 日

--

![](img/ef5651e85d6ce9e99f406012546b1fd0.png)

图形语法就像一套构建模块 —— 图片来自 [Nik Shuliahin 💛💙](https://unsplash.com/@tjump?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

早在 1999 年，已故的 Leland Wilkinson 就写了他具有开创性的著作 *The Grammar of Graphics*[1]，在书中他解释了可以从类似于书面语言语法的构建模块中构建图表这一概念。

根据 H2O.ai 在他们对 Wilkinson 的精彩 [致敬](https://h2o.ai/leland/)（也是他成为首席科学家的地方）中提到，“图形语法提供了一种新的方式来创建和描述数据可视化，它是一个语言 —— 或者说语法 —— 用于指定图表中的视觉元素，这一全新的理念从根本上塑造了现代数据可视化。”

十年后，出现了可能是这一思想最著名的实现 —— *ggplot2*，这是由新西兰学者、现为 RStudio 首席科学家的 [Hadley Wickham](https://en.wikipedia.org/wiki/Hadley_Wickham) 开发的 R 语言图表库。他在论文 [A Layered Grammar of Graphics](https://vita.had.co.nz/papers/layered-grammar.pdf) 和他的书籍 *ggplot2*[2] 中解释了 ggplot2。*ggplot2* 已成为最受欢迎的 R 包之一。

如果你是一个 Python 爱好者，你可能会觉得 *ggplot2* 和图形语法与你关系不大，因为……
