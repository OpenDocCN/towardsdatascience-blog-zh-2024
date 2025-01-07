# 线性代数的鸟瞰图：秩-零度定理及行秩为何等于列秩

> 原文：[`towardsdatascience.com/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075?source=collection_archive---------13-----------------------#2024-01-30`](https://towardsdatascience.com/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075?source=collection_archive---------13-----------------------#2024-01-30)

## 为什么所有矩阵的独立行向量的数量必须与独立列向量的数量完全相等？

[](https://medium.com/@rohitpandey576?source=post_page---byline--bc084e0e1075--------------------------------)![Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--bc084e0e1075--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bc084e0e1075--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bc084e0e1075--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--bc084e0e1075--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bc084e0e1075--------------------------------) ·20 分钟阅读·2024 年 1 月 30 日

--

![](img/e55b72573acad26ccb659a414e804591.png)

图片来自作者。由 midjourney 制作。

这是《线性代数的鸟瞰图》这本正在编写的书的第五章。到目前为止的目录：

+   [第一章：基础知识](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-the-basics-29ad2122d98f)

+   第二章：[映射的度量——行列式](https://medium.com/p/1e5fd752a3be)

+   [**第三章：** 为什么矩阵乘法是这样的？](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)

+   [第四章：矩阵链乘法](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-matrix-chain-multiplication-a718748c7fd5)

+   第五章：[方程组、线性回归与神经网络](https://medium.com/p/fe5b88a57f66)

+   第六章：秩与零度及为何行秩 == 列秩

+   第七章：[左右逆矩阵 => 单射-满射映射](https://medium.com/towards-data-science/birds-eye-view-of-linear-algebra-left-right-inverse-injective-surjective-maps-621988c874bd)

+   第八章（当前）：[正交矩阵](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-orthonormal-matrices-856a2ca040aa)

在之前的章节中，我们讨论了现代机器学习和人工智能应用是如何通过将图像、文本、音频等各种对象编码到高维向量空间中来工作的。而我们为处理高维向量空间所开发的基本工具是线性代数。
