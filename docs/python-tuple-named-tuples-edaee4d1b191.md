# Python “元组+”：具名元组

> 原文：[https://towardsdatascience.com/python-tuple-named-tuples-edaee4d1b191?source=collection_archive---------2-----------------------#2024-01-03](https://towardsdatascience.com/python-tuple-named-tuples-edaee4d1b191?source=collection_archive---------2-----------------------#2024-01-03)

## PYTHON 编程

## 元组是一个强大的 Python 数据类型——但具名元组更强大！

[](https://medium.com/@nyggus?source=post_page---byline--edaee4d1b191--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--edaee4d1b191--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--edaee4d1b191--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--edaee4d1b191--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--edaee4d1b191--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--edaee4d1b191--------------------------------) ·阅读时长 25 分钟·2024年1月3日

--

![](../Images/9ae2459dfa5ba52377719be16f261147.png)

具名元组结合了名称和元组的优点。图片由 [Ainur Iman](https://unsplash.com/@mynameisiknow?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Python 中三种最常用的数据类型是列表、字典和元组。列表和字典是可变的，这意味着它们的元素在创建后可以被更改。而元组则是不可变的，创建后不能更改。如果你确实需要修改元组的内容，你必须创建一个新实例，并将其分配给相同的变量。

本文聚焦于 Python 的具名元组，具名元组是一种特殊的元组类型，它将普通元组的强大功能与具名字段的灵活性相结合。与普通元组相比，具名元组可以使代码更简洁、更易读且更易维护——甚至更具 Python 风格。然而，使用具名元组时需要小心，因为有时过度使用具名元组反而会降低代码的可读性，而非提高它。

继续阅读以了解更多！

要理解具名元组，你首先需要理解普通的 Python 元组。如果你不熟悉元组，强烈建议你先阅读以下两篇关于这种数据类型的文章：
