# Python 中的 LEGB 规则是什么？为什么它很重要？

> 原文：[https://towardsdatascience.com/what-is-the-python-legb-rule-why-it-is-important-1fdcfecfd62f?source=collection_archive---------2-----------------------#2024-04-22](https://towardsdatascience.com/what-is-the-python-legb-rule-why-it-is-important-1fdcfecfd62f?source=collection_archive---------2-----------------------#2024-04-22)

![](../Images/56634f6d455e4db026522206bf11a43e.png)

由作者在 Canva 创建的图片

## 命名空间和解析顺序有助于提高 Python 编程的性能和健壮性

[](https://christophertao.medium.com/?source=post_page---byline--1fdcfecfd62f--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--1fdcfecfd62f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1fdcfecfd62f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1fdcfecfd62f--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--1fdcfecfd62f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1fdcfecfd62f--------------------------------) ·10 分钟阅读·2024年4月22日

--

像大多数其他编程语言一样，Python 也有不同的作用域，在这些作用域中变量和函数被定义。当然，存在一些规则和层级，以确保这些作用域之间的行为是确定的。例如，我们无法在外部访问函数内部的变量。

在我看来，Python 明确定义了对象的作用域，并且直观易懂。这使得它具备了易学易用的特点。然而，如果 Python 开发者希望提升技能，并从新手成长为专家，**LEGB** 规则是不可忽视的。

在本文中，我将介绍 LEGB 规则，它描述了 Python 中不同命名空间的逻辑和行为。之后，还会给出一些技巧和最佳实践。因此，本文不仅仅是解释概念，还会提供基于规则得出的设计含义和技巧。如果你仍然不确定 LEGB 规则，不要错过！

# 1\. 什么是 Python 中的 LEGB 规则？
