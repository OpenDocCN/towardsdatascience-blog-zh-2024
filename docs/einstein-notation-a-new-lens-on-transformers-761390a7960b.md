# 爱因斯坦符号：Transformer 新视角

> 原文：[`towardsdatascience.com/einstein-notation-a-new-lens-on-transformers-761390a7960b?source=collection_archive---------1-----------------------#2024-11-20`](https://towardsdatascience.com/einstein-notation-a-new-lens-on-transformers-761390a7960b?source=collection_archive---------1-----------------------#2024-11-20)

## 转换 Transformer 模型的数学

[](https://medium.com/@ch.mittendorf?source=post_page---byline--761390a7960b--------------------------------)![Dr. Christoph Mittendorf](https://medium.com/@ch.mittendorf?source=post_page---byline--761390a7960b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--761390a7960b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--761390a7960b--------------------------------) [Dr. Christoph Mittendorf](https://medium.com/@ch.mittendorf?source=post_page---byline--761390a7960b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--761390a7960b--------------------------------) ·8 分钟阅读·2024 年 11 月 20 日

--

![](img/27d78cf2967592bbfc4e2308237ab91d.png)

Transformer（由作者使用 FLUX1-schnell 创建）

在本文中，我们将通过一段有趣的旅程，探索 Transformer 世界，利用爱因斯坦符号解开其架构的复杂性。

## **引言**：

Transformer 模型在自然语言处理（及其他领域）中引发了革命，在各种任务上都取得了最先进的成果。它们表现出色，但其底层数学运算可能复杂且难以理解，尤其是如果没有分解每一层的操作。在本文中，我提议使用爱因斯坦符号来表达 Transformer 模型中的数学运算。

请注意，爱因斯坦符号通常用于物理学和数学领域，如广义相对论、电磁学、量子力学和流体力学，也用于线性代数中，以更紧凑的形式表示矩阵运算。

目标是以简洁优雅的方式编写每一层的数学运算。通过利用对重复指标的隐式求和，爱因斯坦符号可以简化张量运算的表示，使其（可能）更容易理解，从而更容易实现 Transformer 模型的各个层次…
