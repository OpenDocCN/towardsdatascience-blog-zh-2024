# 速度需求：Streamlit 与 Functool 缓存

> 原文：[https://towardsdatascience.com/need-for-speed-streamlit-vs-functool-caching-eb3b7426f209?source=collection_archive---------4-----------------------#2024-08-28](https://towardsdatascience.com/need-for-speed-streamlit-vs-functool-caching-eb3b7426f209?source=collection_archive---------4-----------------------#2024-08-28)

## 比较 pandas 和 polars 中 streamlit 缓存与 functools 缓存的性能。结果会让你惊讶！

[](https://medium.com/@joparga3?source=post_page---byline--eb3b7426f209--------------------------------)[![Jose Parreño](../Images/707d5179926d36fba257f5476494e10e.png)](https://medium.com/@joparga3?source=post_page---byline--eb3b7426f209--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--eb3b7426f209--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--eb3b7426f209--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--eb3b7426f209--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eb3b7426f209--------------------------------) ·12 分钟阅读·2024年8月28日

--

![](../Images/97ee58b0ef7f87fbf177d262debc264b.png)

图片由 [Oscar Sutton](https://unsplash.com/@o5ky?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/red-car-blowing-turbo-pBrHNFqcX-M?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

[Streamlit](https://streamlit.io/) 是我构建概念验证演示和分析仪表盘的默认框架。该框架的简洁性允许快速开发和容易维护。然而，简洁性的暗面是，它带有内置的设计假设，使其很难作为一款顶级生产工具使用。我们稍后会详细讨论这些问题，但这些假设的结果是，Streamlit 在处理和渲染应用时可能会***非常慢***。

在这篇文章中，我想向你展示**提高 Streamlit 应用速度的 2 种方法**：使用内置的**Streamlit 缓存功能**和使用内置的**functools 缓存功能**。这两种方法都基于缓存的概念，即如果某个操作之前已经执行过，那么输出会被保存下来以便之后重用。

在进入结果之前，我认为理解以下 3 个基本理论是很重要的：Streamlit 是如何工作的，Streamlit 缓存如何工作，以及 functools 缓存是如何在后台运行的。

*附言：所有图片均由我创作，除非另有说明。*

# Streamlit 会重新执行所有内容……
