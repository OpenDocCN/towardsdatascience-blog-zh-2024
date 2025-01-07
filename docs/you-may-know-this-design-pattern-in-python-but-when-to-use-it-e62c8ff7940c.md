# 你可能已经知道 Python 中的这个设计模式，但该何时使用它呢？

> 原文：[`towardsdatascience.com/you-may-know-this-design-pattern-in-python-but-when-to-use-it-e62c8ff7940c?source=collection_archive---------5-----------------------#2024-03-31`](https://towardsdatascience.com/you-may-know-this-design-pattern-in-python-but-when-to-use-it-e62c8ff7940c?source=collection_archive---------5-----------------------#2024-03-31)

![](img/9cc611bb9a0181b91a6978e19ef2a5e1.png)

图片来自 [Pexels](https://pixabay.com/users/pexels-2286921/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1846774) 自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1846774)

## 从教程到 Python 单例设计模式的实用示例

[](https://christophertao.medium.com/?source=post_page---byline--e62c8ff7940c--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--e62c8ff7940c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e62c8ff7940c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e62c8ff7940c--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--e62c8ff7940c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e62c8ff7940c--------------------------------) ·阅读时长 8 分钟·2024 年 3 月 31 日

--

如果你不再是 Python 的新手，那么有一个非常重要的编程技巧叫做“设计模式”，它是必须掌握的。设计模式通常是解决软件开发中典型问题和需求的最佳实践和最有效的方法。

在本文中，我将介绍一种最流行的设计模式之一——**单例模式**。它是一种特殊的类定义方式，确保一个类在任何时候只能有一个实例。

我将从如何以标准方式编写单例类开始。接下来，本文的主要重点是为你展示一些典型且实用的代码示例。因此，我们将了解什么时候使用它，以及这种设计模式的最佳实践是什么。

# 1\. 如何编写一个基本的单例模式

![](img/cea125b8548748e3c387d43f747018aa.png)

图片来自 [eko pramono](https://pixabay.com/users/nature_design-16152932/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5241253) 自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5241253)

在编写单例类之前，让我们先看看一个普通类。这样，稍后我们就能将其与单例进行比较。

## 1.1 一个普通类
