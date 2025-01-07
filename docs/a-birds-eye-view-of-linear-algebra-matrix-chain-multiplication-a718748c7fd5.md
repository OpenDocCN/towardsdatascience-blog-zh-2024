# 《线性代数一瞥：矩阵链乘法》

> 原文：[`towardsdatascience.com/a-birds-eye-view-of-linear-algebra-matrix-chain-multiplication-a718748c7fd5?source=collection_archive---------0-----------------------#2024-11-13`](https://towardsdatascience.com/a-birds-eye-view-of-linear-algebra-matrix-chain-multiplication-a718748c7fd5?source=collection_archive---------0-----------------------#2024-11-13)

## 线性代数是人工智能的基础。矩阵乘法是线性代数的基本操作。如果存在一整个矩阵链呢？

[](https://medium.com/@rohitpandey576?source=post_page---byline--a718748c7fd5--------------------------------)![Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--a718748c7fd5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a718748c7fd5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a718748c7fd5--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--a718748c7fd5--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a718748c7fd5--------------------------------) ·23 分钟阅读·2024 年 11 月 13 日

--

![](img/6c7ab0ebf9dcaec38e424c80f1f70f94.png)

图像由 MidJourney 创建

这是正在进行中的线性代数书籍的第四章。到目前为止的目录：

+   第一章：基础知识

+   第二章：映射的度量——行列式

+   [**第三章：** 为什么矩阵乘法是这样定义的？](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)

+   第四章（当前章节）：矩阵链乘法

+   第五章：方程组、线性回归与神经网络

+   第六章：秩、虚秩及行秩 == 列秩

我们在[第三章](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)中讨论了矩阵乘法，以及它为什么以这种方式定义。我们还从五种不同的方式进行了可视化展示。花一章的篇幅讨论这个非常重要的操作是值得的，因为它在许多领域都至关重要。

当涉及到两个矩阵时，很快就会涉及到更多的矩阵。在矩阵乘法中，其中一个矩阵通常会分裂成两个，以此类推，直到我们得到一整条需要相乘的矩阵链。而与两个矩阵的情况不同，这时就不再只有一个矩阵，而是……
