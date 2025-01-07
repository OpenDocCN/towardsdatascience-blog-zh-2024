# 深入理解 JAX 中的自动微分

> 原文：[`towardsdatascience.com/understanding-automatic-differentiation-in-jax-a-deep-dive-179bbdf01e87?source=collection_archive---------7-----------------------#2024-10-11`](https://towardsdatascience.com/understanding-automatic-differentiation-in-jax-a-deep-dive-179bbdf01e87?source=collection_archive---------7-----------------------#2024-10-11)

## 解锁梯度：JAX 如何让自动微分感觉像魔法一样

[](https://aamir-khan.medium.com/?source=post_page---byline--179bbdf01e87--------------------------------)![Aamir Mushir Khan](https://aamir-khan.medium.com/?source=post_page---byline--179bbdf01e87--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--179bbdf01e87--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--179bbdf01e87--------------------------------) [Aamir Mushir Khan](https://aamir-khan.medium.com/?source=post_page---byline--179bbdf01e87--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--179bbdf01e87--------------------------------) ·10 分钟阅读·2024 年 10 月 11 日

--

欢迎来到 JAX 的世界，在这里，微分自动发生，速度比凌晨三点的咖啡因驱动程序员还要快！在这篇文章中，我们将深入探讨自动微分（AD）的概念，这是 JAX 的核心功能，我们还将探索为什么它对机器学习、科学计算以及任何需要微分的领域都是一项颠覆性的技术。最近，JAX 的受欢迎程度不断上升，这得益于由可微编程推动的科学机器学习新兴领域。

但等一下——在我们深入探讨之前，让我们先问一些基本问题。

+   什么是 JAX？

+   那么，我们为什么需要自动微分呢？

+   最重要的是，JAX 是如何让这一切变得更酷（也更简单）的？

别担心，你会带着微笑离开， hopefully，并且希望你能在工具箱中增加一个新工具，让你像专业人士一样处理微分问题。准备好了吗？让我们深入了解吧。

# JAX 到底是什么？

JAX 是由 Google 开发的一个库，旨在进行**高性能数值计算**和**机器学习研究**。它的核心功能是使编写**可微分、可并行化并可编译到硬件加速器**（如 GPU 和 TPU）上运行的代码变得非常简单。OG 团队…
