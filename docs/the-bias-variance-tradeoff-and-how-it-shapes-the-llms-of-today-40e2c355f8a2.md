# 偏差-方差权衡及其如何塑造今天的 LLM

> 原文：[https://towardsdatascience.com/the-bias-variance-tradeoff-and-how-it-shapes-the-llms-of-today-40e2c355f8a2?source=collection_archive---------4-----------------------#2024-11-02](https://towardsdatascience.com/the-bias-variance-tradeoff-and-how-it-shapes-the-llms-of-today-40e2c355f8a2?source=collection_archive---------4-----------------------#2024-11-02)

## 低归纳偏差对于构建通用人工智能至关重要吗？

[](https://medium.com/@zakharymg?source=post_page---byline--40e2c355f8a2--------------------------------)[![Michael Zakhary](../Images/8657f728dd52de4094b71635b1c17087.png)](https://medium.com/@zakharymg?source=post_page---byline--40e2c355f8a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40e2c355f8a2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--40e2c355f8a2--------------------------------) [Michael Zakhary](https://medium.com/@zakharymg?source=post_page---byline--40e2c355f8a2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40e2c355f8a2--------------------------------) ·阅读时长 6 分钟·2024年11月2日

--

![](../Images/eb32a758ce0d5d1409030ca8d576fe66.png)

图片由 [BoliviaInteligente](https://unsplash.com/@boliviainteligente?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在今天的机器学习领域，我们发现自己被这些巨大的变换器模型包围，如**chatGPT**和**BERT**，它们在几乎所有下游任务中都表现出无与伦比的性能，但前提是需要先在上游任务上进行大量的预训练。那么，是什么让变换器需要如此多的参数，因此需要大量的训练数据才能发挥作用呢？

这是我想要深入探讨的问题，通过探索 LLM 和数据科学中偏差与方差这一基石主题之间的联系。应该会很有趣！

# 背景

首先，我们需要回顾一下，定义一些基础知识，为接下来的内容做铺垫。

**方差**

方差几乎可以与数据科学中的过拟合同义。这个术语的核心语言选择是“**变化**”的概念。高方差模型是指当输入变量 X 发生微小变化时，目标变量 **Y *变化*** 非常大的模型。

因此，在高方差模型中，X 的微小变化会导致 Y 的巨大响应（这就是为什么 Y 通常被称为响应变量）。在经典的...
