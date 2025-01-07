# 复杂的列表推导式也可以是可读的！

> 原文：[`towardsdatascience.com/complex-list-comprehensions-can-be-readable-b6c657622910?source=collection_archive---------4-----------------------#2024-04-18`](https://towardsdatascience.com/complex-list-comprehensions-can-be-readable-b6c657622910?source=collection_archive---------4-----------------------#2024-04-18)

## PYTHON 编程

## 即使是相当复杂的 Python 推导式，也比对应的 `for` 循环更具可读性。

[](https://medium.com/@nyggus?source=post_page---byline--b6c657622910--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--b6c657622910--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b6c657622910--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b6c657622910--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--b6c657622910--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b6c657622910--------------------------------) ·阅读时间 17 分钟·2024 年 4 月 18 日

--

![](img/7e7f71626f0daf1413ad70503c215a1c.png)

Python 推导式允许在循环中进行强大的计算——即使是嵌套的计算。图片来源：[Önder Örtel](https://unsplash.com/@onderortel?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Python 推导式——包括列表、字典和集合推导式，以及生成器表达式——构成了强大的 Python 语法糖。你可以在以下文章中阅读相关内容：

[](/a-guide-to-python-comprehensions-4d16af68c97e?source=post_page-----b6c657622910--------------------------------) ## Python 推导式指南

### 学习列表推导式（listcomps）、集合推导式（setcomps）、字典推导式的细节…

towardsdatascience.com [](/building-comprehension-pipelines-in-python-ec68dce53d03?source=post_page-----b6c657622910--------------------------------) ## 在 Python 中构建推导管道

### 推导管道是 Python 特有的一种构建管道的方式

towardsdatascience.com

Python 推导式相比于对应的 `for` 循环有两个显著的优势：它们更快，且更具可读性。

注意短语“*可以更具可读性*。”确实，它们并不总是更具可读性。这引出了以下问题：它们在什么情况下可读性更高？
