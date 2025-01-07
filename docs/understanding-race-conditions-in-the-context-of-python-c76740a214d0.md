# 在 Python 中理解竞争条件

> 原文：[https://towardsdatascience.com/understanding-race-conditions-in-the-context-of-python-c76740a214d0?source=collection_archive---------3-----------------------#2024-05-06](https://towardsdatascience.com/understanding-race-conditions-in-the-context-of-python-c76740a214d0?source=collection_archive---------3-----------------------#2024-05-06)

![](../Images/0231a75fb6d343547ed24fa477b2d4f8.png)

该图片由作者在 Canva 中创建

## Python 的 GIL 无法保证线程安全

[](https://christophertao.medium.com/?source=post_page---byline--c76740a214d0--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--c76740a214d0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c76740a214d0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c76740a214d0--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--c76740a214d0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c76740a214d0--------------------------------) ·阅读时长：8分钟·2024年5月6日

--

无论你是一个经常使用线程的 Python 用户，还是一个从未使用过线程技术但未来想尝试的人，有一个概念是我们无法绕过的，那就是线程安全。它指的是许多由多线程引起的微妙错误。尽管我们通过在多个线程中并发运行任务获得了好处，但必须始终牢记线程安全，以避免这些错误。

在所有典型的多线程相关的错误中，竞争条件是一个非常微妙且令人头疼的错误。如果并发运行的线程不多，它可能不会轻易发生，这使得调试变得更加困难。然而，如果我们真正理解其背后的原因，它也是一个可以避免的错误。

在本文中，我将介绍什么是竞争条件以及它为什么会发生。在 Python 中，GIL 使其比大多数其他编程语言更具健壮性，但它仍然可能发生。在某些情况下，竞争条件在 Python 中可能不会发生，但我将找到一种方式来确保重现这个错误，并详细解释其机制。

# 1\. 什么是竞争条件（Race Condition）？
