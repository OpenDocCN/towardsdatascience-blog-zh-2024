# Polars + NVIDIA GPU 教程

> 原文：[`towardsdatascience.com/polars-nvidia-gpu-tutorial-483b83d7f899?source=collection_archive---------5-----------------------#2024-09-17`](https://towardsdatascience.com/polars-nvidia-gpu-tutorial-483b83d7f899?source=collection_archive---------5-----------------------#2024-09-17)

## 使用 Polars 与 NVIDIA GPU 结合可以加速你的数据管道

[](https://ivopbernardo.medium.com/?source=post_page---byline--483b83d7f899--------------------------------)![Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--483b83d7f899--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--483b83d7f899--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--483b83d7f899--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--483b83d7f899--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--483b83d7f899--------------------------------) ·5 分钟阅读·2024 年 9 月 17 日

--

![](img/163b49cc31e9887402e3125d0a3c2ce9.png)

图片来源：[noaa](https://unsplash.com/pt-br/@noaa) @ Unsplash.com

在 Python 中处理庞大数据集一直是一个挑战。Python 语言并非专门为处理大量数据而设计，像原生 SQL 系统或 Spark 那样。

在 Python 中处理二维数据集最著名的库，毫无疑问是*pandas*。虽然易于使用，且每个数据科学家都在使用，但 Pandas 是用 Python 和 C 编写的，使得它在对大数据进行操作时稍显繁琐且较慢。如果你是数据科学家，你一定经历过等待 200 年才能完成一个*group by*操作的痛苦。

旨在解决这个问题的库之一是*polars* ——一个极为高效的 Python 包，能够处理大规模数据集，主要有以下几个原因：

+   它是用 Rust 编写的

+   它自动利用多线程

+   它通过使用懒计算推迟大部分计算

而且……今天之后，你可以利用 NVIDIA 的硬件来最大化*polars*的 GPU 引擎能力。

在这篇博客文章中，我们将看到如何利用*polars+GPU*大幅加速你的数据管道。

# 环境设置
