# 空间索引：镶嵌

> 原文：[https://towardsdatascience.com/spatial-index-tessellation-aca39463fe9f?source=collection_archive---------4-----------------------#2024-06-17](https://towardsdatascience.com/spatial-index-tessellation-aca39463fe9f?source=collection_archive---------4-----------------------#2024-06-17)

## 镶嵌在空间索引中的应用以及Uber H3如何工作

[](https://pyblog.medium.com/?source=post_page---byline--aca39463fe9f--------------------------------)[![Adesh Nalpet Adimurthy](../Images/fb3e0c4d3c3f829fd95bde08f0a4cd57.png)](https://pyblog.medium.com/?source=post_page---byline--aca39463fe9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aca39463fe9f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aca39463fe9f--------------------------------) [Adesh Nalpet Adimurthy](https://pyblog.medium.com/?source=post_page---byline--aca39463fe9f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aca39463fe9f--------------------------------) ·15分钟阅读·2024年6月17日

--

![](../Images/2bc3bc1fd639d4300e9b7ae5f33ae9e3.png)

本文是[空间索引：网格系统](https://medium.com/@pyblog/spatial-index-grid-systems-f02880fb147a)的延续，我们将在其中为镶嵌奠定基础，并深入探讨[Uber H3](https://github.com/uber/h3)的细节。

# 0\. 基础

镶嵌或铺砖是将空间覆盖/划分为更小的、不重叠的形状，这些形状完美地拼接在一起，没有间隙或重叠。在空间索引中，镶嵌用于将地球表面划分为可管理的单元，以便高效地存储数据、查询和分析。

为什么需要地理网格系统（[镶嵌系统](https://www.pyblog.xyz/cartograms-documentation#tessellation)）：现实世界充满了各种地理元素，既有自然的也有人工的，它们都没有遵循任何一致的结构。为了对这些元素进行地理算法或分析，我们需要一种更抽象的形式。

地图是一个很好的起点，也是最常见的抽象形式，大多数人都熟悉它。然而，地图仍然包含各种不一致性。这就需要一个网格系统，它将杂乱的地理空间转化为一个更加干净和结构化的数学空间，使得执行计算和查询变得更加容易。
