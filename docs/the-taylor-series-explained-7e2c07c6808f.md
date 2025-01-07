# 泰勒级数，解释

> 原文：[`towardsdatascience.com/the-taylor-series-explained-7e2c07c6808f?source=collection_archive---------3-----------------------#2024-09-11`](https://towardsdatascience.com/the-taylor-series-explained-7e2c07c6808f?source=collection_archive---------3-----------------------#2024-09-11)

## 函数逼近的方法

[](https://medium.com/@xichu.zhang?source=post_page---byline--7e2c07c6808f--------------------------------)![Xichu Zhang](https://medium.com/@xichu.zhang?source=post_page---byline--7e2c07c6808f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7e2c07c6808f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e2c07c6808f--------------------------------) [Xichu Zhang](https://medium.com/@xichu.zhang?source=post_page---byline--7e2c07c6808f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e2c07c6808f--------------------------------) ·17 分钟阅读·2024 年 9 月 11 日

--

![](img/a9748f78fbacbd8ee50d1a7b66ccdbb2.png)

作者提供的图片

如果你对泰勒级数感到愤怒，并且对它的[无用性](https://www.reddit.com/r/EngineeringStudents/comments/gbo8tm/taylor_series_can_fuck_off/)感到惊讶，仿佛它存在的唯一目的是为我们制造解题任务，那么这篇文章可能适合你。

## 简介：数列与级数

本节内容用于刷新一些基础知识。首先是**级数和数列**的定义。有限级数是通过加总有限数列的项得到的。但在实分析的背景下，这些有限级数通常会被舍弃，因为如果没有无穷，就无法谈论收敛性、收敛速度等内容，而这些在实分析中是非常基础的。

在无限级数的情况下，存在着无数个来自无尽级数的项。到任何指定项的和称为**部分和**。[2]

数列的概念非常简单，它是一个按特定模式排列的数字列表，形式如下：*a_1*，*a_2*，*a_3*，…*a_n*。但把它看作一个**函数**会更有帮助，这给出了数列的正式定义：

> 数列是一个函数 f: ℕ → ℝ， 其中 ℕ 是非负整数的集合（所以包括 0），ℝ 是所有实数的集合。
