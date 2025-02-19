# KAN：它为什么以及如何工作？深入探讨

> 原文：[`towardsdatascience.com/kan-why-and-how-does-it-work-a-deep-dive-1adab4837fa3?source=collection_archive---------0-----------------------#2024-05-10`](https://towardsdatascience.com/kan-why-and-how-does-it-work-a-deep-dive-1adab4837fa3?source=collection_archive---------0-----------------------#2024-05-10)

## 我们能通过 KAN 发现新的物理学吗？

[](https://saptashwa.medium.com/?source=post_page---byline--1adab4837fa3--------------------------------)![Saptashwa Bhattacharyya](https://saptashwa.medium.com/?source=post_page---byline--1adab4837fa3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1adab4837fa3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1adab4837fa3--------------------------------) [Saptashwa Bhattacharyya](https://saptashwa.medium.com/?source=post_page---byline--1adab4837fa3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1adab4837fa3--------------------------------) ·阅读时长 13 分钟·2024 年 5 月 10 日

--

![](img/ea5d35d82a620a8bec456b82cd565f47.png)

神经网络能发现新的物理学吗？（由作者使用 DALLE-2 生成）

上周，在我们参加的一个 AI 与物理学会议（[EuCAIFCon](https://indico.nikhef.nl/event/4875/)）上，很多讨论围绕基础模型展开，讨论是否有可能通过 AI 发现潜在的新物理定律，结果不出所料：最近有一篇关于 KAN 的论文¹在 arXiv 上发表，讨论了通过神经网络发现/重新发现物理学和数学模型的可能性。

我在周末有时间阅读了这篇令人着迷的论文的一部分，接下来我们将深入探讨构建 KAN 的数学、公式和背后的思想。我们还将安装 KAN，检查 MIT 研究人员在论文中展示的许多令人惊叹的结果之一。

我们将详细了解数学符号和概念，以便理解为什么这个网络引起了如此大的轰动！

让我们开始吧。

# KA 表示定理：

KAN 或 Kolmogorov-Arnold 网络是基于著名数学家 Kolmogorov 和 Arnold 的表示定理。因此，首先让我们回顾一下这个定理。

KA 定理是由这些数学家提出，用于研究 [Hilbert 的第十三问题](https://en.wikipedia.org/wiki/Hilbert%27s_thirteenth_problem)：David Hilbert 提出有必要…
