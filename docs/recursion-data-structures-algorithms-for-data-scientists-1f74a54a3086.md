# 递归 — 数据结构与算法为数据科学家

> 原文：[`towardsdatascience.com/recursion-data-structures-algorithms-for-data-scientists-1f74a54a3086?source=collection_archive---------3-----------------------#2024-11-03`](https://towardsdatascience.com/recursion-data-structures-algorithms-for-data-scientists-1f74a54a3086?source=collection_archive---------3-----------------------#2024-11-03)

## 递归，递归，递归，递归，递归，等等。

[](https://medium.com/@egorhowell?source=post_page---byline--1f74a54a3086--------------------------------)![Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--1f74a54a3086--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1f74a54a3086--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1f74a54a3086--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--1f74a54a3086--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1f74a54a3086--------------------------------) ·阅读时间：5 分钟·2024 年 11 月 3 日

--

![](img/93d128b8dc4f8f1e838c2bd0031bab27.png)

摄影师：[Voicu Apostol](https://unsplash.com/@cerpow?utm_source=medium&utm_medium=referral) 摄影，图片来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

递归是计算机科学中最著名的概念之一，因为它非常有趣！

在本文中，我将解释递归及其不同类型，并向你展示一些著名的例子。

# 什么是递归？

递归是指一个函数调用自身，但输入通常会发生变化。所以，当函数调用自身时，它就被称为递归函数。

你本质上是在将问题拆解成更小的子问题，这些子问题独立解决，但会一步一步地合并起来。

几乎每个递归函数都可以用循环格式来编写，但递归的框架通常更加优雅！

[俄罗斯套娃](https://en.wikipedia.org/wiki/Matryoshka_doll)可以看作是递归的一个例子，因为每个娃娃中包含另一个娃娃，然后那个娃娃又包含另一个，依此类推。

递归技术上可以无限进行下去，但通常会有一些停止条件来防止这种情况发生。否则，计算机的内存会很快用完！

一般来说，一个递归函数有两个要素：

+   ***基本情况*** *— 一个不需要递归的终止情景。*
