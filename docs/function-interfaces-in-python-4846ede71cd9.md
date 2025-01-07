# Python 中的函数接口

> 原文：[https://towardsdatascience.com/function-interfaces-in-python-4846ede71cd9?source=collection_archive---------4-----------------------#2024-03-19](https://towardsdatascience.com/function-interfaces-in-python-4846ede71cd9?source=collection_archive---------4-----------------------#2024-03-19)

## PYTHON 编程

## 在 Python 中，函数接口可以通过类型提示和协议来实现。

[](https://medium.com/@nyggus?source=post_page---byline--4846ede71cd9--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--4846ede71cd9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4846ede71cd9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4846ede71cd9--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--4846ede71cd9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4846ede71cd9--------------------------------) ·阅读时间 18 分钟 ·2024 年 3 月 19 日

--

![](../Images/ad64319b4ec547bf3013a6c0a4185166.png)

图片由 [Rubaitul Azad](https://unsplash.com/@rubaitulazad?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

虽然 Python 并没有提供与其他编程语言中常见的接口相同的功能，但它提供了类似的功能。传统的接口是 [抽象基类](https://docs.python.org/3/library/abc.html)（ABCs），可以通过 `abc` 模块使用。后来，[类型提示](https://docs.python.org/3/library/typing.html) 和 [类型协议](https://docs.python.org/3/library/typing.html#typing.Protocol)（`typing.Protocol`）被添加进来。

抽象基类（ABCs）和类型协议作为 Python 定义类的期望结构和行为的机制，类似于其他编程语言中的接口。尽管 Python 本身并未正式使用“接口”这一术语来描述此功能，但这一概念与 Python 的“协议”非常契合。事实上，在 `typing.Protocol` 被添加到 typing 模块之前，Python 就已使用“协议”一词来描述接口。因此，Python 的动态特性允许创建动态接口，而类型提示和类型协议则从静态检查的角度促进了接口的规范化。我们将在本文中讨论这一点。

抽象基类和类型协议通常在创建自定义类时考虑：你可以创建一个抽象基类或一个...
