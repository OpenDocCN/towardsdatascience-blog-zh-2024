# AI 的 CUDA —— 直观且详尽的解释

> 原文：[https://towardsdatascience.com/cuda-for-ai-intuitively-and-exhaustively-explained-6ba6cb4406c5?source=collection_archive---------0-----------------------#2024-06-14](https://towardsdatascience.com/cuda-for-ai-intuitively-and-exhaustively-explained-6ba6cb4406c5?source=collection_archive---------0-----------------------#2024-06-14)

## 人工智能 | GPU 编程 | AI 理论

## 从零开始的并行化 AI

[](https://medium.com/@danielwarfield1?source=post_page---byline--6ba6cb4406c5--------------------------------)[![Daniel Warfield](../Images/c1c8b4dd514f6813e08e401401324bca.png)](https://medium.com/@danielwarfield1?source=post_page---byline--6ba6cb4406c5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6ba6cb4406c5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6ba6cb4406c5--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--6ba6cb4406c5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6ba6cb4406c5--------------------------------) ·47 分钟阅读·2024年6月14日

--

![](../Images/bf4b3333a98af836a0e396e8fdc92ed4.png)

“Thread Master”由 Daniel Warfield 使用 Midjourney 创建。除非另有说明，所有图片均由作者提供。文章最初发布于 [Intuitively and Exhaustively Explained](https://iaee.substack.com/)。

在这篇文章中，我们将使用 CUDA 在 GPU 上训练 AI 模型，基本上是从零开始实现 AI，几乎不需要任何先验知识。

首先，我们将探讨现代计算机的一些核心组件，然后深入 GPU，讲解它是什么、如何工作以及它为何对 AI 很有用。接下来，我们将介绍 CUDA，解释它是什么以及它如何使我们能够编写利用 CPU 和 GPU 的应用程序。在理解 CUDA 编程的基本原理之后，我们将使用 CUDA 来构建、训练并测试一个用于分类任务的神经网络。

**这篇文章适合谁？** 任何想深入理解 AI 的人。

**这篇文章有多高阶？** 鉴于其内容较为高级，本篇文章可能更适合具有一定机器学习经验的人。如果你没有机器学习经验，阅读这篇文章你也一定会学到很多。只需要一节一节地读，遇到不懂的就多查查 Google。

**前提条件：** 基本的软件开发技能。有一定的 C++ 接触可能会有帮助，但并不是必须的。它……
