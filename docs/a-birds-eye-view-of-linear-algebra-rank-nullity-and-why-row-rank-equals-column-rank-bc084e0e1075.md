# 线性代数的鸟瞰图：秩-零度定理及行秩为何等于列秩

> 原文：[https://towardsdatascience.com/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075?source=collection_archive---------13-----------------------#2024-01-30](https://towardsdatascience.com/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075?source=collection_archive---------13-----------------------#2024-01-30)

## 为什么所有矩阵的独立行向量的数量必须与独立列向量的数量完全相等？

[](https://medium.com/@rohitpandey576?source=post_page---byline--bc084e0e1075--------------------------------)[![Rohit Pandey](../Images/af817d8f68f2984058f0afb8fd7ecbe9.png)](https://medium.com/@rohitpandey576?source=post_page---byline--bc084e0e1075--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bc084e0e1075--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bc084e0e1075--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--bc084e0e1075--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bc084e0e1075--------------------------------) ·20分钟阅读·2024年1月30日

--

![](../Images/e55b72573acad26ccb659a414e804591.png)

图片来自作者。由midjourney制作。

这是《线性代数的鸟瞰图》这本正在编写的书的第五章。到目前为止的目录：

+   [第1章：基础知识](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-the-basics-29ad2122d98f)

+   第2章：[映射的度量——行列式](https://medium.com/p/1e5fd752a3be)

+   [**第3章：** 为什么矩阵乘法是这样的？](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)

+   [第4章：矩阵链乘法](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-matrix-chain-multiplication-a718748c7fd5)

+   第5章：[方程组、线性回归与神经网络](https://medium.com/p/fe5b88a57f66)

+   第6章：[秩与零度及为何行秩 == 列秩](/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075)

+   第7章：[左右逆矩阵 => 单射-满射映射](https://medium.com/towards-data-science/birds-eye-view-of-linear-algebra-left-right-inverse-injective-surjective-maps-621988c874bd)

+   第8章（当前）：[正交矩阵](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-orthonormal-matrices-856a2ca040aa)

在之前的章节中，我们讨论了现代机器学习和人工智能应用是如何通过将图像、文本、音频等各种对象编码到高维向量空间中来工作的。而我们为处理高维向量空间所开发的基本工具是线性代数。
