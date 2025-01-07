# 构建可扩展的数据平台

> 原文：[`towardsdatascience.com/building-scalable-data-platforms-6621c9bde515?source=collection_archive---------0-----------------------#2024-09-01`](https://towardsdatascience.com/building-scalable-data-platforms-6621c9bde515?source=collection_archive---------0-----------------------#2024-09-01)

## 数据平台设计中的数据网格趋势

[](https://mshakhomirov.medium.com/?source=post_page---byline--6621c9bde515--------------------------------)![💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--6621c9bde515--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6621c9bde515--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6621c9bde515--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--6621c9bde515--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6621c9bde515--------------------------------)·阅读 13 分钟·2024 年 9 月 1 日

--

![](img/ddcb02532abcb22a06317d41a42f2b17.png)

使用[Kandinsky](https://github.com/ai-forever/Kandinsky-2)生成的 AI 图像

在本文中，我旨在深入研究各种数据平台架构，更好地了解它们的演变、优势、劣势和实际应用。重点将放在数据网格架构上，以及它在现代数据堆栈（MDS）和当今数据驱动的景观中的作用。

众所周知，数据平台的架构深刻影响其性能和可扩展性。挑战通常在于选择一个最符合您特定业务需求的架构。

鉴于市场上现有的大量数据工具，很容易迷失方向。我时常看到关于这个主题的互联网文章往往高度推测性。关于哪些工具最好，谁领导行业，以及如何做出正确选择的问题可能非常令人沮丧。这个故事是为那些想要了解更多关于数据平台设计以及在每种情况下选择哪种的数据从业者而写的。

## 现代数据堆栈

我几乎在互联网上的每个与数据相关的网站上都听到这个术语。每个 LinkedIn 数据组都提供了十几篇关于这个主题的帖子。然而，其中大多数只涵盖了数据工具，没有强调重要性…
