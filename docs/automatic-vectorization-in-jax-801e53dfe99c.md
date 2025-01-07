# JAX中的自动向量化

> 原文：[https://towardsdatascience.com/automatic-vectorization-in-jax-801e53dfe99c?source=collection_archive---------8-----------------------#2024-10-24](https://towardsdatascience.com/automatic-vectorization-in-jax-801e53dfe99c?source=collection_archive---------8-----------------------#2024-10-24)

## 让循环飞走吧！

[](https://aamir-khan.medium.com/?source=post_page---byline--801e53dfe99c--------------------------------)[![Aamir Mushir Khan](../Images/31607cb450fa99cfbefb3b56f2d3a05d.png)](https://aamir-khan.medium.com/?source=post_page---byline--801e53dfe99c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--801e53dfe99c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--801e53dfe99c--------------------------------) [Aamir Mushir Khan](https://aamir-khan.medium.com/?source=post_page---byline--801e53dfe99c--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--801e53dfe99c--------------------------------) ·阅读时间8分钟·2024年10月24日

--

JAX因其在数学计算和机器学习中的速度、效率和灵活性而闻名。但它其中一个鲜为人知的超级能力——可以让你摆脱无休止的循环和模板代码——就是**自动向量化**。

![](../Images/4105381630a03bd37916bbf62f68e894.png)

图片由[Hamish](https://unsplash.com/@el_ham?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你曾经写过处理数组或批量数据的代码，你就知道优化并行化是多么繁琐。但通过JAX的`**vmap**`（向量化映射）函数，你可以告别丑陋的循环，迎来简洁、高效并行化的代码。

在本文中，我们将深入探讨JAX中的自动向量化。我们将探索向量化是如何工作的，为什么它对加速计算至关重要，以及如何利用JAX的`vmap`避免写显式的循环。在此过程中，我们将通过一些真实世界的例子，并通过代码展示如何让你更加喜爱JAX。

准备好了吗？我们出发吧！

# 向量化到底是怎么回事？

在我们深入了解JAX的具体内容之前，先来谈谈**向量化**的一般概念。在传统编程中，你可能会写出逐一处理每个数据点的循环代码。例如，如果你想对数组中的每个元素应用一个函数，你可能会使用`for`循环……
