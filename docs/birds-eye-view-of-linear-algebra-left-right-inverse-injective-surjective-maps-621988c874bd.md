# 线性代数的鸟瞰图：左逆、右逆 => 单射、满射映射

> 原文：[https://towardsdatascience.com/birds-eye-view-of-linear-algebra-left-right-inverse-injective-surjective-maps-621988c874bd?source=collection_archive---------9-----------------------#2024-12-03](https://towardsdatascience.com/birds-eye-view-of-linear-algebra-left-right-inverse-injective-surjective-maps-621988c874bd?source=collection_archive---------9-----------------------#2024-12-03)

## 如果矩阵乘法不是可交换的，那么为什么我们没有左右逆？

[](https://medium.com/@rohitpandey576?source=post_page---byline--621988c874bd--------------------------------)[![Rohit Pandey](../Images/af817d8f68f2984058f0afb8fd7ecbe9.png)](https://medium.com/@rohitpandey576?source=post_page---byline--621988c874bd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--621988c874bd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--621988c874bd--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--621988c874bd--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--621988c874bd--------------------------------) ·10分钟阅读·2024年12月3日

--

![](../Images/fca5d4c8f37dd99e7a21f2bba9d49468.png)

图片来源：midjourney

注：除非另有说明，所有图片均为作者提供。

这是正在进行的线性代数书籍的第七章：“线性代数的鸟瞰图”。到目前为止的目录：

+   [第1章：基础知识](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-the-basics-29ad2122d98f)

+   第2章：[映射的度量——行列式](https://medium.com/p/1e5fd752a3be)

+   [**第3章：** 为什么矩阵乘法是这样设计的？](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)

+   [第4章：矩阵链乘法](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-matrix-chain-multiplication-a718748c7fd5)

+   第5章：[方程组、线性回归与神经网络](https://medium.com/p/fe5b88a57f66)

+   第6章：[秩与零度，为什么行秩等于列秩](/a-birds-eye-view-of-linear-algebra-rank-nullity-and-why-row-rank-equals-column-rank-bc084e0e1075)

+   第7章：[左逆与右逆 => 单射与满射](https://medium.com/towards-data-science/birds-eye-view-of-linear-algebra-left-right-inverse-injective-surjective-maps-621988c874bd)

+   第8章（当前）：[正交归一矩阵](https://medium.com/towards-data-science/a-birds-eye-view-of-linear-algebra-orthonormal-matrices-856a2ca040aa)

我们在[第3章](/a-birds-eye-view-of-linear-algebra-why-is-matrix-multiplication-like-that-a4d94067651e)中深入讨论了矩阵乘法。我们提到过矩阵乘法有一个单位元，即矩阵：
