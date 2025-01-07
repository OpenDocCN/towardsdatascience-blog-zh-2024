# 停止手动排序你的 Python 列表，如果性能是你的关注点

> 原文：[`towardsdatascience.com/stop-manually-sorting-your-list-in-python-if-performance-is-concerned-fad2832e4fe5?source=collection_archive---------5-----------------------#2024-08-29`](https://towardsdatascience.com/stop-manually-sorting-your-list-in-python-if-performance-is-concerned-fad2832e4fe5?source=collection_archive---------5-----------------------#2024-08-29)

![](img/caa5e53852e34b711f137dfa5a95e885.png)

图片由作者在 Canva 上制作

## 一个与 C 扩展一样快速的排序集合库

[](https://christophertao.medium.com/?source=post_page---byline--fad2832e4fe5--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--fad2832e4fe5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fad2832e4fe5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fad2832e4fe5--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--fad2832e4fe5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fad2832e4fe5--------------------------------) ·阅读时间：8 分钟 ·2024 年 8 月 29 日

--

至少对我来说，大多数时候在用 Python 编程时，我都在处理集合类型。这包括原生的 Python 列表、集合和字典，以及一些第三方集合类型，如 Numpy 数组和 TensorFlow 张量。后者通常要快得多，因为它们是使用 CPython 或其他与 C 相关的扩展实现的。

然而，有时候使用它们会让人感到不知所措，特别是当我们正在开发一个与数据科学无关的应用时，这些库可能会显得过于庞大，仅仅为了提高性能而引入可能并不划算。

在本文中，我将介绍一个名为 Sorted Containers 的库，当需要一个排序的集合类型时，它提供了最佳实践。我将涵盖这个库中的两种主要类型：排序列表和排序集合。我会通过一些实际的例子来帮助理解。

在开始之前，确保你已经通过`pip`安装了这个库。

```py
pip install sortedcontainers
```

# 1\. 排序列表 — 实时排行榜
