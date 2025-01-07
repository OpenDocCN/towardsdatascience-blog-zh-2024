# 用 Rust 编写 LLM：寻找高效的矩阵乘法

> 原文：[https://towardsdatascience.com/writing-llms-in-rust-looking-for-an-efficient-matrix-multiplication-e9539b0cb9d3?source=collection_archive---------4-----------------------#2024-11-14](https://towardsdatascience.com/writing-llms-in-rust-looking-for-an-efficient-matrix-multiplication-e9539b0cb9d3?source=collection_archive---------4-----------------------#2024-11-14)

## 从 Karpathy 的 `llm.c` 开始，我不禁想：“我能用 Rust 写这个吗？”以下是我学到的经验，以及我如何编写 `llm.rust`。在这篇文章中，我们来解决矩阵乘法问题。

[](https://stefanobosisio1.medium.com/?source=post_page---byline--e9539b0cb9d3--------------------------------)[![Stefano Bosisio](../Images/450d904024a4cbf1adf8a625886d852e.png)](https://stefanobosisio1.medium.com/?source=post_page---byline--e9539b0cb9d3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e9539b0cb9d3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e9539b0cb9d3--------------------------------) [Stefano Bosisio](https://stefanobosisio1.medium.com/?source=post_page---byline--e9539b0cb9d3--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e9539b0cb9d3--------------------------------) ·阅读时长 14 分钟·2024年11月14日

--

![](../Images/1769e0bfb8a5da7e50ac3f79dba6ce6c.png)

图片来自 [GoogleDeepMind](https://unsplash.com/@googledeepmind) 在 [Unsplash](https://unsplash.com/photos/a-bonsai-tree-growing-out-of-a-concrete-block-K2V_fqM2RY8)

矩阵乘法可能是机器学习中最重要的操作。我仍然记得当我还是一名工程学学生时，在一次线性代数的课堂上，老师开始讲解矩阵、特征向量、基和正交基。我当时非常困惑，花了些时间才开始理解我们为什么如此关注矩阵和基集合，以及一个好的基对我们世界意味着什么。从那时起，我便对线性代数产生了浓厚的兴趣，并且从纯粹的计算机科学角度来看，所有那些试图在处理矩阵时变得越来越高效的算法，真的非常惊人。

特别是，我们知道矩阵-向量乘积相对简单，但当我们处理矩阵-矩阵或张量-张量乘积时，问题变得越来越复杂。从这里开始，许多方法已经被实现以优化矩阵乘法。例如，很久以前我发布了关于 [DeepMind](https://medium.com/towards-data-science/understanding-deepmind-matrix-multiplication-c8dc49687ce7)的文章……
