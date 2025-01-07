# Python 装饰器非常有用，但什么时候使用它们呢？

> 原文：[`towardsdatascience.com/python-decoration-is-very-useful-but-when-to-use-them-e4f5faab43ec?source=collection_archive---------1-----------------------#2024-05-12`](https://towardsdatascience.com/python-decoration-is-very-useful-but-when-to-use-them-e4f5faab43ec?source=collection_archive---------1-----------------------#2024-05-12)

![](img/2ed738355e0e9dfa070aefb0fef08c5a.png)

图片来源于 Canva.com

## 总结了 Python 装饰器的五种典型使用模式

[](https://christophertao.medium.com/?source=post_page---byline--e4f5faab43ec--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--e4f5faab43ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e4f5faab43ec--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4f5faab43ec--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--e4f5faab43ec--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4f5faab43ec--------------------------------) ·阅读时间：9 分钟·2024 年 5 月 12 日

--

作为 Python 最具特色的功能之一，装饰器可能并不容易理解。然而，无论你是数据科学家、数据工程师还是 Web 开发者，它都会为我们的开发工作提供极大的便利。

几年前，我写了一篇关于 Python 装饰器的教程文章。如果你需要了解一些关于 Python 装饰器的基础内容，强烈推荐阅读那篇文章。

[](/the-simplest-tutorial-for-python-decorator-dadbf8f20b0f?source=post_page-----e4f5faab43ec--------------------------------) ## Python 装饰器最简单的教程

### 添加一个“Pythonic”的包装器到任何函数/类，而不修改它们

towardsdatascience.com

在本文中，我将总结 Python 装饰器的五种不同使用场景。希望能让你了解我们在何时以及为何应该使用装饰器。

# 1\. 快速入门 — 函数装饰器

![](img/6d46c20f494326eedcb92f9c7c1f5caa.png)

图片来源于 Canva.com

让我们从 Python 装饰器的基本使用模式开始，也就是函数装饰器。
