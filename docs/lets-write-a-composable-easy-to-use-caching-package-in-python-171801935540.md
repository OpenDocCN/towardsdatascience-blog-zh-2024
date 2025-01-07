# 让我们在Python中编写一个可组合的、易于使用的缓存包

> 原文：[https://towardsdatascience.com/lets-write-a-composable-easy-to-use-caching-package-in-python-171801935540?source=collection_archive---------11-----------------------#2024-08-29](https://towardsdatascience.com/lets-write-a-composable-easy-to-use-caching-package-in-python-171801935540?source=collection_archive---------11-----------------------#2024-08-29)

## 简单、用户友好的缓存，满足您所有的需求

[](https://mikehuls.medium.com/?source=post_page---byline--171801935540--------------------------------)[![Mike Huls](../Images/8f9f55a0d25db00799c5d37383b7f5b6.png)](https://mikehuls.medium.com/?source=post_page---byline--171801935540--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--171801935540--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--171801935540--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--171801935540--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--171801935540--------------------------------) ·9分钟阅读·2024年8月29日

--

![](../Images/c5c0b29c65a5b610fca4844dc0182202.png)

Python选择缓存策略（图片由ChatGPT提供 + 作者进行的业余编辑）

在本文中，我们将逐步讲解从零开始构建Python缓存系统的过程。目标是……

+   更好地理解缓存并探索各种缓存策略

+   了解为什么我们选择了组合方式而非继承方式

+   学习如何以及何时有效地应用缓存

我们将重点构建一个**用户友好的**包，使您能够**轻松地将缓存**添加到您的代码中。此外，我们还将提供一种**简单的方式来扩展缓存**，通过为其提供自定义行为。让我们开始编码吧！

# 在我们开始之前……

本文详细介绍了如何创建缓存，这些缓存是[PyPI上的Cachr包](https://pypi.org/project/cachr/)的一部分。所有代码都可以在这个[Github仓库](https://github.com/mike-huls/cachr)中找到。**欢迎任何贡献**；随时提交错误报告、错误修复、功能请求、文档改进或增强功能！
