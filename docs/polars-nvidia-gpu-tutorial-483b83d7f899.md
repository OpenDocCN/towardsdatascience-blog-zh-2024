# Polars + NVIDIA GPU教程

> 原文：[https://towardsdatascience.com/polars-nvidia-gpu-tutorial-483b83d7f899?source=collection_archive---------5-----------------------#2024-09-17](https://towardsdatascience.com/polars-nvidia-gpu-tutorial-483b83d7f899?source=collection_archive---------5-----------------------#2024-09-17)

## 使用Polars与NVIDIA GPU结合可以加速你的数据管道

[](https://ivopbernardo.medium.com/?source=post_page---byline--483b83d7f899--------------------------------)[![Ivo Bernardo](../Images/39887b6f3e63a67c0545e87962ad5df0.png)](https://ivopbernardo.medium.com/?source=post_page---byline--483b83d7f899--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--483b83d7f899--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--483b83d7f899--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--483b83d7f899--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--483b83d7f899--------------------------------) ·5分钟阅读·2024年9月17日

--

![](../Images/163b49cc31e9887402e3125d0a3c2ce9.png)

图片来源：[noaa](https://unsplash.com/pt-br/@noaa) @ Unsplash.com

在Python中处理庞大数据集一直是一个挑战。Python语言并非专门为处理大量数据而设计，像原生SQL系统或Spark那样。

在Python中处理二维数据集最著名的库，毫无疑问是*pandas*。虽然易于使用，且每个数据科学家都在使用，但Pandas是用Python和C编写的，使得它在对大数据进行操作时稍显繁琐且较慢。如果你是数据科学家，你一定经历过等待200年才能完成一个*group by*操作的痛苦。

旨在解决这个问题的库之一是*polars* ——一个极为高效的Python包，能够处理大规模数据集，主要有以下几个原因：

+   它是用Rust编写的

+   它自动利用多线程

+   它通过使用懒计算推迟大部分计算

而且……今天之后，你可以利用NVIDIA的硬件来最大化*polars*的GPU引擎能力。

在这篇博客文章中，我们将看到如何利用*polars+GPU*大幅加速你的数据管道。

# 环境设置
