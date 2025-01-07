# 开源数据可观察性与Elementary — 从零到英雄（第一部分）

> 原文：[https://towardsdatascience.com/open-source-data-observability-with-elementary-from-zero-to-hero-part-1-23d5e98b68db?source=collection_archive---------11-----------------------#2024-09-10](https://towardsdatascience.com/open-source-data-observability-with-elementary-from-zero-to-hero-part-1-23d5e98b68db?source=collection_archive---------11-----------------------#2024-09-10)

## 一份我在刚开始时希望能拥有的循序渐进的实操指南

[](https://sezin-sezgin.medium.com/?source=post_page---byline--23d5e98b68db--------------------------------)[![Sezin Sezgin-Rummelsberger](../Images/7892210e427388de3b5066a4b250d96d.png)](https://sezin-sezgin.medium.com/?source=post_page---byline--23d5e98b68db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--23d5e98b68db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--23d5e98b68db--------------------------------) [Sezin Sezgin-Rummelsberger](https://sezin-sezgin.medium.com/?source=post_page---byline--23d5e98b68db--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--23d5e98b68db--------------------------------) ·阅读时长6分钟·2024年9月10日

--

数据可观察性及其重要性常常被讨论和撰写成现代数据和分析工程的重要方面。市场上有许多具有各种功能和价格的工具可供选择。在这篇两部分的文章中，我们将重点介绍开源版本的Elementary，这个平台是众多数据可观察性工具之一，专为dbt量身定制，并与之无缝对接。我们将从零开始设置，并计划在第二部分结束时理解其工作原理以及在不同数据场景下的可行性。在开始之前，我还想声明，我与Elementary没有任何关联，所有观点均为我个人的看法。

在第一部分中，我们将设置Elementary并检查如何读取Elementary的每日报告。如果你已经对这部分感到熟悉，并且有兴趣查看不同类型的数据测试以及哪种测试最适合哪种场景，你可以直接跳到第二部分：

+   [开源数据可观察性与Elementary — 从零到英雄（第二部分）](https://medium.com/@sezin-sezgin/opensource-data-observability-with-elementary-from-zero-to-hero-part-2-c09077ed4476)

我已经使用Elementary有一段时间了，我作为数据工程师的经验是积极的，我的团队对结果的看法也很正面。我们的团队使用Elementary进行自动化的每日监控，配合自托管的Elementary仪表盘。Elementary还拥有一个非常方便的云平台……
