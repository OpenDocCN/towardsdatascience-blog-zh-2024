# Python 的并行范式转变

> 原文：[`towardsdatascience.com/pythons-parallel-paradigm-shift-2bb40d3c2dd0?source=collection_archive---------1-----------------------#2024-07-22`](https://towardsdatascience.com/pythons-parallel-paradigm-shift-2bb40d3c2dd0?source=collection_archive---------1-----------------------#2024-07-22)

![](img/f700ad20e272a5dedb6b7a27e49a6c28.png)

图像由 AI（Dalle-3）提供

## 探索无 GIL Python 的性能潜力

[](https://medium.com/@thomas_reid?source=post_page---byline--2bb40d3c2dd0--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--2bb40d3c2dd0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2bb40d3c2dd0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2bb40d3c2dd0--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--2bb40d3c2dd0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2bb40d3c2dd0--------------------------------) ·13 分钟阅读·2024 年 7 月 22 日

--

## 发生了什么？

Python 中最受期待的变化之一（移除全局解释器锁——见 PEP 703）现在已经可以进行测试了。目前的 Python 预发布版本（3.13.0b4）提供了实验性支持，可以在构建时使用`--disable-gil`标志禁用全局解释器锁。使用此标志构建的 Python 版本也被称为`**自由线程**`版本。

> **注意：此项工作仍在进行中，因此会有错误发生。请勿在生产环境中使用此版本。**

在本文中，我将向你展示如何构建这个版本的 Python，并通过启用与禁用 GIL 的代码示例，展示其对运行时间的影响。

## 为什么这很重要？

简而言之，**“性能”**。

由于自由线程执行可以同时利用系统上的所有可用核心，代码通常会运行得更快。对于数据科学家、机器学习或数据工程师而言，这不仅适用于你的代码，还适用于构建你所依赖的系统、框架和库的代码。

许多机器学习和数据科学任务是 CPU 密集型的，特别是在模型…
