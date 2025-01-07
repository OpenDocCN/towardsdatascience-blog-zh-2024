# 从零开始学习高斯过程

> 原文：[`towardsdatascience.com/gaussian-processes-from-scratch-cfc11715730c?source=collection_archive---------4-----------------------#2024-01-06`](https://towardsdatascience.com/gaussian-processes-from-scratch-cfc11715730c?source=collection_archive---------4-----------------------#2024-01-06)

## 通过仅用 NumPy 实现高斯过程，深入理解它们。

[](https://medium.com/@theo.wolf?source=post_page---byline--cfc11715730c--------------------------------)![Theo Wolf](https://medium.com/@theo.wolf?source=post_page---byline--cfc11715730c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfc11715730c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfc11715730c--------------------------------) [Theo Wolf](https://medium.com/@theo.wolf?source=post_page---byline--cfc11715730c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfc11715730c--------------------------------) ·阅读时间 6 分钟 ·2024 年 1 月 6 日

--

# 引言

高斯过程（GPs）是一类非常强大的模型。在机器学习算法中，很少有能在提供准确不确定性度量的同时，又保持高度灵活性的算法。问题是，高斯过程在概念上非常难以理解。大多数解释都使用了一些复杂的代数和概率知识，这些知识通常对于理解这些模型如何运作并不十分有用。

也有许多很棒的指南跳过了数学部分，直接给出了这些模型如何工作的直观理解，但当你自己在正确的语境下使用高斯过程（GPs）时，我个人认为仅有表面知识是远远不够的。这就是为什么我想从零开始带你一步步实现一个简化版的高斯过程，帮助你更清楚地理解所有那些为你实现这些模型的库背后究竟发生了什么。

我还会提供我的 [GitHub 仓库](https://github.com/TheodoreWolf/gps_numpy)链接，在那里你可以找到仅使用 NumPy 实现的高斯过程代码。我尽量将数学抽象化，但显然，仍然有一些数学知识是不可避免的……

# 数据

第一步始终是查看数据。我们将使用月度 CO2 大气浓度随时间变化的数据，这些数据是来自马纳洛亚天文台的测量，是高斯过程常用的数据集之一 [1]。这是故意选择的同一个数据集……
