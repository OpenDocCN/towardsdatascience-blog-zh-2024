# 自动化提示工程：终极实用指南

> 原文：[https://towardsdatascience.com/automated-prompt-engineering-the-definitive-hands-on-guide-1476c8cd3c50?source=collection_archive---------0-----------------------#2024-09-04](https://towardsdatascience.com/automated-prompt-engineering-the-definitive-hands-on-guide-1476c8cd3c50?source=collection_archive---------0-----------------------#2024-09-04)

## 学习如何自动化提示工程，并在LLM工作负载中解锁显著的性能提升

[](https://heiko-hotz.medium.com/?source=post_page---byline--1476c8cd3c50--------------------------------)[![Heiko Hotz](../Images/d08394d46d41d5cd9e76557a463be95e.png)](https://heiko-hotz.medium.com/?source=post_page---byline--1476c8cd3c50--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1476c8cd3c50--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1476c8cd3c50--------------------------------) [Heiko Hotz](https://heiko-hotz.medium.com/?source=post_page---byline--1476c8cd3c50--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1476c8cd3c50--------------------------------) ·阅读时长21分钟·2024年9月4日

--

![](../Images/107d464c61389543bd7204aabee8d9ce.png)

作者提供的图片——使用Imagen 3创建

# 这篇文章讲什么？

自动化提示工程（APE）是一种自动化生成和优化大型语言模型（LLM）提示的技术，旨在提高模型在特定任务上的表现。它使用提示工程的概念，手动构建和测试各种提示，并将整个过程自动化。如我们将看到的，它与传统监督式机器学习中的自动化超参数优化***非常***相似。

在本教程中，我们将深入探讨自动化提示工程（APE）：首先，我们将了解其原理，探讨一些生成提示的策略，以及其他相关技术，如示例选择。然后，我们将进入动手操作部分，从零开始编写一个APE程序，即我们不会使用像DSPy这样的库来为我们完成这些工作。通过这样做，我们将更好地理解APE的原理，并能更好地利用那些可以直接提供此功能的框架。
