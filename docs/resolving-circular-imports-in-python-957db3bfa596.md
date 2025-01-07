# 解决 Python 中的循环导入问题

> 原文：[https://towardsdatascience.com/resolving-circular-imports-in-python-957db3bfa596?source=collection_archive---------5-----------------------#2024-04-25](https://towardsdatascience.com/resolving-circular-imports-in-python-957db3bfa596?source=collection_archive---------5-----------------------#2024-04-25)

## PYTHON 编程

## 了解并解决循环导入问题，这是当两个模块相互导入时常见的错误。

[](https://medium.com/@nyggus?source=post_page---byline--957db3bfa596--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--957db3bfa596--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--957db3bfa596--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--957db3bfa596--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--957db3bfa596--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--957db3bfa596--------------------------------) ·阅读时间 7 分钟·2024年4月25日

--

![](../Images/17cfc72a6324bc236ca2020fc41b2bd6.png)

循环导入会导致一个永无止境的循环。照片来自[Matt Seymour](https://unsplash.com/@mattseymour?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上。

循环导入在 Python 应用程序和包的开发过程中相当常见。当两个模块尝试同时相互导入时，就会发生这种错误——这会创建一个循环，导致两个模块都无法完全加载。因此，导致的错误是`ImportError`，大致表示无法从部分初始化的模块中导入对象。

解决这个错误通常是直接的。然而，有时代码需要重新设计和重构——有时需要相当广泛的重构。本文旨在通过解释解决该错误的方法来帮助您。

# 解决方案

我们将探索在不重构代码以改变其行为的情况下，解决循环导入的方法。有时，只需重新定义类的职责，使其不直接相互依赖即可。但这种方法通常不足以解决问题。因此，我们将重点关注解决循环导入的策略，而不改变导致循环导入的模块中定义的对象的行为。
