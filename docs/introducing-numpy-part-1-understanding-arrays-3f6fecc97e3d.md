# 介绍 NumPy，第一部分：理解数组

> 原文：[`towardsdatascience.com/introducing-numpy-part-1-understanding-arrays-3f6fecc97e3d?source=collection_archive---------5-----------------------#2024-09-10`](https://towardsdatascience.com/introducing-numpy-part-1-understanding-arrays-3f6fecc97e3d?source=collection_archive---------5-----------------------#2024-09-10)

## 快速成功的数据科学

## 创建、描述和访问属性

[](https://medium.com/@lee_vaughan?source=post_page---byline--3f6fecc97e3d--------------------------------)![Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--3f6fecc97e3d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3f6fecc97e3d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3f6fecc97e3d--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--3f6fecc97e3d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3f6fecc97e3d--------------------------------) ·阅读时长 19 分钟·2024 年 9 月 10 日

--

![](img/f0e8c61e5a658a187f8cf8202e38fbc0.png)

DALL-E3 想象的 3D 数组

欢迎来到 *介绍 NumPy* 系列，这是一个面向 Python（或 NumPy）初学者的四部分教程。目标是通过展示 NumPy 的核心功能，配合表格和实际操作示例，帮助读者理解 NumPy。

NumPy，简称 *Numerical Python*，是 Python 用于数值计算的基础库。它扩展了 Python 的数学能力，并为许多科学和数学包奠定了基础。

NumPy 增强了 *Python 标准库* 中的内置工具，而这些工具对许多数据分析计算来说可能过于简单。使用 NumPy，您可以执行快速操作，包括数学运算、排序、选择、I/O、离散傅里叶变换、基本线性代数、基本统计操作、形状操作、随机模拟等。

NumPy 的核心是 *数组数据结构*，它是一个值的网格，构成了 NumPy 功能的核心。通过利用预编译的 C 代码，NumPy 提高了较慢算法的性能，并高效地执行复杂的数学计算。通过支持多维数组和基于数组的操作，NumPy 简化了处理…
