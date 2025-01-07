# 在你的苹果硅芯片 MacBook 上进行 LoRA 微调

> 原文：[`towardsdatascience.com/lora-fine-tuning-on-your-apple-silicon-macbook-432c7dab614a?source=collection_archive---------5-----------------------#2024-11-20`](https://towardsdatascience.com/lora-fine-tuning-on-your-apple-silicon-macbook-432c7dab614a?source=collection_archive---------5-----------------------#2024-11-20)

## 让我们一步步在你的 MacBook 上进行微调

[](https://medium.com/@mgunton7?source=post_page---byline--432c7dab614a--------------------------------)![Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--432c7dab614a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--432c7dab614a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--432c7dab614a--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--432c7dab614a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--432c7dab614a--------------------------------) ·阅读时间：10 分钟·2024 年 11 月 20 日

--

![](img/1b79c5fc5ce91eab4ddd3add262ff070.png)

作者图片 — Flux.1

随着模型变得越来越小，我们看到越来越多的消费级计算机能够在本地运行 LLMs。这大大降低了人们训练自己模型的门槛，并且可以尝试更多的训练技术。

一款能够在本地良好运行大型语言模型（LLMs）的消费级计算机是苹果的 Mac。苹果利用其定制的硅片，创建了一个名为 MLX 的阵列处理库。通过使用 MLX，苹果能够比许多其他消费级计算机更好地运行 LLMs。

在这篇博客文章中，我将高层次地解释 MLX 是如何工作的，然后展示如何使用 MLX 在本地微调你自己的 LLM。最后，我们将使用量化技术加速我们微调后的模型。

让我们深入了解吧！

# MLX 背景

## 什么是 MLX（谁可以使用它？）

MLX 是苹果推出的一个开源库，允许 Mac 用户更高效地运行包含大量张量的程序。自然地，当我们想要训练或微调模型时，这个库就非常有用。

MLX 的工作原理是通过在中央处理单元（CPU）、图形处理单元（GPU）之间非常高效地进行内存传输来实现的……
