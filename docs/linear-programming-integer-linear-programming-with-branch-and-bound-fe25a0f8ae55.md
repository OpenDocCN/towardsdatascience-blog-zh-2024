# 线性规划：带分支定界的整数线性规划

> 原文：[`towardsdatascience.com/linear-programming-integer-linear-programming-with-branch-and-bound-fe25a0f8ae55?source=collection_archive---------13-----------------------#2024-11-19`](https://towardsdatascience.com/linear-programming-integer-linear-programming-with-branch-and-bound-fe25a0f8ae55?source=collection_archive---------13-----------------------#2024-11-19)

## 第四部分：将线性规划优化扩展到离散决策变量

[](https://medium.com/@jarom.hulet?source=post_page---byline--fe25a0f8ae55--------------------------------)![Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--fe25a0f8ae55--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe25a0f8ae55--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe25a0f8ae55--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--fe25a0f8ae55--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe25a0f8ae55--------------------------------) ·9 分钟阅读·2024 年 11 月 19 日

--

![](img/4f66234cfccf1ba9f98b6efb29dc6d06.png)

图片来自 Pexels.com，摄影：Min An

在本系列之前的内容中，我们讨论了严格的线性规划——其中目标函数、约束条件和决策变量都是线性且连续的。这种线性设置具有一些非常好的特性，但它的灵活性较差。本文将讨论如何使用一种名为*整数线性规划*（ILP）的工具，允许使用离散决策变量。

这是我写的关于线性规划系列文章的第四篇。其他文章（包括一个介绍部分——如果你不熟悉线性规划，可以查看）可以在这里找到：

![Jarom Hulet](img/44595b6052adc15ed442ed9da5c7bb33.png)

[Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page-----fe25a0f8ae55--------------------------------)

## 线性规划

[查看列表](https://medium.com/@jarom.hulet/list/linear-programming-fe5c1fba2583?source=post_page-----fe25a0f8ae55--------------------------------)4 篇故事![](img/a98c79af17d5bf4693bde9f4e8ea5d91.png)![](img/30d0f2e268559f848a7316d87b8ffa1e.png)![](img/aafe65ad5cd07aa337c7a2e78dfdad6f.png)

本文将涵盖以下主题：

1.  何时需要离散决策变量

1.  分支定界算法如何解决整数线性规划问题

1.  整数线性规划的优缺点…
