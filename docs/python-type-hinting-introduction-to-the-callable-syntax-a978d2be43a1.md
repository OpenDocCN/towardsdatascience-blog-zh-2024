# Python 类型提示：可调用语法简介

> 原文：[`towardsdatascience.com/python-type-hinting-introduction-to-the-callable-syntax-a978d2be43a1?source=collection_archive---------10-----------------------#2024-05-08`](https://towardsdatascience.com/python-type-hinting-introduction-to-the-callable-syntax-a978d2be43a1?source=collection_archive---------10-----------------------#2024-05-08)

## PYTHON 编程

## collections.abc.Callable 语法看起来可能有些复杂。了解如何在实际的 Python 编程中使用它。

[](https://medium.com/@nyggus?source=post_page---byline--a978d2be43a1--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--a978d2be43a1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a978d2be43a1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a978d2be43a1--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--a978d2be43a1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a978d2be43a1--------------------------------) ·14 分钟阅读·2024 年 5 月 8 日

--

![](img/9d2207b4f8962b14ec04c72639792dde.png)

图片来源：[Susan Q Yin](https://unsplash.com/@syinq?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在数据科学中，类型提示已经成为 Python 编程的标准，特别是在生产代码中。我在以下文章中讨论了这个问题：

[](/python-type-hinting-in-data-science-projects-a-must-a-maybe-or-a-no-no-d76b8a53e37b?source=post_page-----a978d2be43a1--------------------------------) ## Python 类型提示在数据科学项目中的应用：必须、可能还是完全不能用？

### 我们是否应该在使用 Python 实现的数据科学项目中使用类型提示？

towardsdatascience.com

同样，数据科学中的 Python 代码使用了大量的可调用对象。我们已经讨论过 Python 中的可调用对象：

[](/python-callables-the-basics-and-the-secrets-ba88bf0729aa?source=post_page-----a978d2be43a1--------------------------------) ## Python 可调用对象：基础与奥秘

### 了解 Python 可调用对象的强大功能。

[towardsdatascience.com

可调用对象无疑是 Python 中一个重要的组成部分。著名的例子当然是函数，但除了函数之外，Python 还提供了其他可调用对象，即定义了`__call__()`方法的类及其实例。
