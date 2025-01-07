# Python 与下划线 (_)

> 原文：[`towardsdatascience.com/python-and-the-underscore-82d7ce8706d?source=collection_archive---------3-----------------------#2024-04-30`](https://towardsdatascience.com/python-and-the-underscore-82d7ce8706d?source=collection_archive---------3-----------------------#2024-04-30)

## PYTHON 编程

## 本文讨论了下划线在 Python 中所扮演的各种角色。

[](https://medium.com/@nyggus?source=post_page---byline--82d7ce8706d--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--82d7ce8706d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--82d7ce8706d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82d7ce8706d--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--82d7ce8706d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82d7ce8706d--------------------------------) ·10 分钟阅读·2024 年 4 月 30 日

--

![](img/7c706d662cecaf8befe3f7446ac4fcfe.png)

你会在 Python 代码中看到很多下划线形式的横线。图片由[Jussara Romão](https://unsplash.com/@jussararomao?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)。

下划线——也就是 `_` 这个字符——在 Python 编程中扮演着重要且有时相当特定的角色。每个 Python 程序员都应该意识到它的多功能性，并理解如何有效地在编码中使用下划线。从提高可读性和管理私有属性到在数据处理和国际化中启用特定功能，下划线不仅仅是一个普通字符；它是 Python 语法中最重要的字符之一，成为 Python 语言中的基础工具。

本文分析了下划线在 Python 中的不同角色，探索了使得 `_` 成为 Python 语言中一个必不可少字符的常见及冷门用法。无论你是初学者还是高级程序员，理解下划线的用途将显著提升你的编码技能。

# 下划线的使用案例

## 命名

或许下划线最常见且最重要的用途是在命名中。根据[PEP 8](https://peps.python.org/pep-0008/)，

> 函数名称应使用小写字母，并根据需要用下划线分隔单词以提高可读性。
