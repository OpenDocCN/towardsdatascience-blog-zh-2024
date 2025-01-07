# 使用 Python 线程提升你的编码技能

> 原文：[https://towardsdatascience.com/level-up-your-coding-skills-with-python-threading-8f1bd06b9476?source=collection_archive---------3-----------------------#2024-11-27](https://towardsdatascience.com/level-up-your-coding-skills-with-python-threading-8f1bd06b9476?source=collection_archive---------3-----------------------#2024-11-27)

![](../Images/c0583e9f19a60078de7cb7565c397b23.png)

图片由[Sonika Agarwal](https://unsplash.com/@sonika_agarwal?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 学习如何在机器学习项目中使用队列、守护线程和事件

[](https://medium.com/@marcellopoliti?source=post_page---byline--8f1bd06b9476--------------------------------)[![Marcello Politi](../Images/484e44571bd2e75acfe5fef3146ab3c2.png)](https://medium.com/@marcellopoliti?source=post_page---byline--8f1bd06b9476--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8f1bd06b9476--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8f1bd06b9476--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--8f1bd06b9476--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8f1bd06b9476--------------------------------) ·阅读时间：7分钟·2024年11月27日

--

## 引言

在大多数机器学习工作中，你并不会研究如何改进某个模型架构或设计一个新的损失函数。大多数时候，你必须利用已有的技术并将其适应你的使用场景。因此，优化你的项目在架构设计和实现方面非常重要。一切从这里开始：你需要的是最优的代码，简洁、可重用并且尽可能快地运行。线程是 Python 内置的原生库，但人们并不像应该的那样频繁使用它。

## 关于线程

> 线程是让一个[程序](https://simple.wikipedia.org/wiki/Computer_program)将自己*拆分*为两个或更多同时（或伪同时）运行的[任务](https://simple.wikipedia.org/wiki/Task_(computers))的一种方式……通常，线程存在于一个进程中，同一进程中的不同线程共享相同的资源。

在这篇文章中，我们不会讨论多进程，但 Python 的多进程库与多线程库非常相似。通常：

+   多线程非常适合 I/O 密集型任务，例如在 for 循环中调用 API

+   多进程用于 CPU 密集型任务，例如……
