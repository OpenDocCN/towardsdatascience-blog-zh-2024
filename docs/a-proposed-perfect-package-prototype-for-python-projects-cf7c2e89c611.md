# 提议的 Python 项目完美包原型

> 原文：[`towardsdatascience.com/a-proposed-perfect-package-prototype-for-python-projects-cf7c2e89c611?source=collection_archive---------7-----------------------#2024-06-18`](https://towardsdatascience.com/a-proposed-perfect-package-prototype-for-python-projects-cf7c2e89c611?source=collection_archive---------7-----------------------#2024-06-18)

## 如何构建 Python 包项目的结构，以确保效率、效果和未来的可扩展性

[](https://grahamharrison-86487.medium.com/?source=post_page---byline--cf7c2e89c611--------------------------------)![Graham Harrison](https://grahamharrison-86487.medium.com/?source=post_page---byline--cf7c2e89c611--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cf7c2e89c611--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf7c2e89c611--------------------------------) [Graham Harrison](https://grahamharrison-86487.medium.com/?source=post_page---byline--cf7c2e89c611--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf7c2e89c611--------------------------------) ·14 分钟阅读·2024 年 6 月 18 日

--

![](img/8129e996abf25c961497accf8962d8bc.png)

图片由 [Luke Heibert](https://unsplash.com/@lukeheibert?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/a-lot-of-brown-boxes-that-are-open-gthSas4oYC0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

在设计 Python 包的结构时，有许多选项需要考虑，也有许多决策需要做出，但一旦这些设计选择确定下来，就很难做出根本性的结构变化。

此外，如果用户已经开始在他们的项目中嵌入命名空间，那么你所做的任何更改都意味着这些用户需要更改他们的源代码。

因此，在一开始就做出良好的设计和布局决策对于你的包来说至关重要。

## 问题

组织 Python 包有很多选项，因此找到合适的设计（至少是不会导致后续问题的设计）可能是一个挑战。

## 机遇

如果能够建立一个标准的包布局，解决所有挑战，并在一个骨架项目中进行原型设计，那么创建未来的包将变得非常快捷简单，可以直接复制粘贴。

## 前进的道路
