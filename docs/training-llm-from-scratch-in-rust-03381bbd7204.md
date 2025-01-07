# 在Rust中从零开始训练LLM

> 原文：[https://towardsdatascience.com/training-llm-from-scratch-in-rust-03381bbd7204?source=collection_archive---------2-----------------------#2024-12-26](https://towardsdatascience.com/training-llm-from-scratch-in-rust-03381bbd7204?source=collection_archive---------2-----------------------#2024-12-26)

## 在这篇配套文章中，我将展示我在Rust中从零开始训练一个类似GPT的模型的实现。没有GPU，只有CPU，且性能比本地C代码提高了30倍。

[](https://stefanobosisio1.medium.com/?source=post_page---byline--03381bbd7204--------------------------------)[![Stefano Bosisio](../Images/450d904024a4cbf1adf8a625886d852e.png)](https://stefanobosisio1.medium.com/?source=post_page---byline--03381bbd7204--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--03381bbd7204--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--03381bbd7204--------------------------------) [Stefano Bosisio](https://stefanobosisio1.medium.com/?source=post_page---byline--03381bbd7204--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03381bbd7204--------------------------------) ·阅读时长14分钟·2024年12月26日

--

![](../Images/b2d10b30f55a0cd780f399191ce3b8e9.png)

图片来源：[GoogleDeepMind](https://unsplash.com/@googledeepmind) via [Unsplash](https://unsplash.com/photos/a-close-up-of-a-metal-object-on-a-white-surface-yyl3iDuS3s8)

在我上一篇[文章](https://medium.com/towards-data-science/writing-llms-in-rust-looking-for-an-efficient-matrix-multiplication-e9539b0cb9d3)中，我介绍了矩阵乘法的问题，注意力算法如何使用矩阵乘法来执行平均处理，以及如何在Rust中高效实现——或者至少对我而言——矩阵乘法函数，并使用[Blas](https://docs.rs/blas/latest/blas/)。

在这篇新文章中，我想展示我在用Rust实现[llm.c](https://github.com/karpathy/llm.c)的第一个构建块，即从零开始使用Rust训练一个类似GPT的模型。这是我了解Rust生态系统并理解它与C语言的可比性的方式。特别是，**我希望我的代码能够在仅使用CPU的情况下，从GPT权重开始训练一个类似GPT的模型**——也就是不使用GPU或TPU。我的目标是理解我们在简单笔记本电脑上能够推多大这些模型，以及Rust生态系统在这方面能发挥多大作用。**最终，这段代码也可能对基于给定输入语料库微调GPT模型有所帮助**。

所有相关的代码片段可以在[这里](https://github.com/Steboss/llm.rust)找到。
