# 如何利用缓存技术提升Python性能

> 原文：[https://towardsdatascience.com/how-to-boost-the-performance-of-python-using-the-caching-techniques-156368d191e8?source=collection_archive---------1-----------------------#2024-04-29](https://towardsdatascience.com/how-to-boost-the-performance-of-python-using-the-caching-techniques-156368d191e8?source=collection_archive---------1-----------------------#2024-04-29)

![](../Images/a8f0242aa656e3bb0aa2c2c3a90d3292.png)

图片由作者在Canva中制作

## Python中的高级但开箱即用的缓存装饰器

[](https://christophertao.medium.com/?source=post_page---byline--156368d191e8--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--156368d191e8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--156368d191e8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--156368d191e8--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--156368d191e8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--156368d191e8--------------------------------) ·8分钟阅读·2024年4月29日

--

一旦你不再是Python新手，就该开始探索Python中的内置特性了。我敢打赌，Python中有许多开箱即用的内置特性会让你惊讶。今天，我将在本文中介绍其中一个。

你是否遇到过需要多次执行某个函数且一些结果可以复用的场景？在下面的第一个示例中，你将看到缓存机制使得递归函数的性能提升了120倍。因此，在本文中，我将介绍自Python 3.2版本以来内置的`lru_cache`装饰器。

在`functools`模块中，还有一个装饰器叫做`cache`，它更为直观。然而，易于使用有时意味着灵活性较差。因此，我将从`cache`装饰器开始，介绍它的局限性。接下来，我将重点讲解`lru_cache`如何为我们提供更多的灵活性。

# 1\. @cache装饰器回顾

![](../Images/cb2b78c9c69e86ae837c9d94003e0560.png)

图片由作者在Canva中制作
