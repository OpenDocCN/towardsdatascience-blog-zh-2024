# 线性代数的鸟瞰图：正交归一矩阵

> 原文：[https://towardsdatascience.com/a-birds-eye-view-of-linear-algebra-orthonormal-matrices-856a2ca040aa?source=collection_archive---------0-----------------------#2024-12-24](https://towardsdatascience.com/a-birds-eye-view-of-linear-algebra-orthonormal-matrices-856a2ca040aa?source=collection_archive---------0-----------------------#2024-12-24)

## 正交归一矩阵：线性代数中最优雅的矩阵

[](https://medium.com/@rohitpandey576?source=post_page---byline--856a2ca040aa--------------------------------)[![Rohit Pandey](../Images/af817d8f68f2984058f0afb8fd7ecbe9.png)](https://medium.com/@rohitpandey576?source=post_page---byline--856a2ca040aa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--856a2ca040aa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--856a2ca040aa--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--856a2ca040aa--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--856a2ca040aa--------------------------------) ·17分钟阅读·2024年12月24日

--

![](../Images/1abc1613c2493b14d731ee84818d7743.png)

图片由midjourney创建

这是正在进行中的线性代数书籍《线性代数的鸟瞰图》的第八章。目前的目录如下：

+   [第一章：基础知识](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-the-basics-29ad2122d98f)

+   第二章：[映射的度量 — 行列式](https://medium.com/p/1e5fd752a3be)

+   [**第三章：** 为什么矩阵乘法是这样定义的？](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)

+   [第四章：矩阵链乘法](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-matrix-chain-multiplication-a718748c7fd5)

+   第五章：[方程组、线性回归与神经网络](https://medium.com/p/fe5b88a57f66)

+   第六章：[秩与零度，为什么行秩 == 列秩](/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075)

+   第七章：[左右逆 => 单射-满射映射](https://medium.com/towards-data-science/birds-eye-view-of-linear-algebra-left-right-inverse-injective-surjective-maps-621988c874bd)

+   第八章（当前）：[正交归一矩阵](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-orthonormal-matrices-856a2ca040aa)

在本章中，我们将讨论特殊类型的矩阵：正交矩阵和正交规范矩阵。这些类型的矩阵（以及它们所表示的对应线性映射）具有良好的性质，从理论到数值计算，使得它们易于使用。例如，要获取正交规范矩阵的逆，你只需将其翻转（取其转置）。但我们现在有些超前了。让我们先理解什么是正交规范矩阵。
