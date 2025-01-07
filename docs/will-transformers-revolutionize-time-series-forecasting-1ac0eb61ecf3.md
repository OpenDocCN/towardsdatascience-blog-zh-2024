# Transformer 是否会彻底改变时间序列预测？

> 原文：[https://towardsdatascience.com/will-transformers-revolutionize-time-series-forecasting-1ac0eb61ecf3?source=collection_archive---------2-----------------------#2024-07-31](https://towardsdatascience.com/will-transformers-revolutionize-time-series-forecasting-1ac0eb61ecf3?source=collection_archive---------2-----------------------#2024-07-31)

## 结合来自工业界、学术界和领先研究人员的数据

[](https://medium.com/@nikoskafritsas?source=post_page---byline--1ac0eb61ecf3--------------------------------)[![Nikos Kafritsas](../Images/de965cfcd8fbd8e1baf849017d365cbb.png)](https://medium.com/@nikoskafritsas?source=post_page---byline--1ac0eb61ecf3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ac0eb61ecf3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1ac0eb61ecf3--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--1ac0eb61ecf3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ac0eb61ecf3--------------------------------) ·阅读时间 9 分钟·2024 年 7 月 31 日

--

![](../Images/e56cc4aeee1808c90d4458dc5c78d043.png)

由作者使用 DALLE*3 创作

**“生成式 AI 革命”的核心是 Transformer 模型——由谷歌于 2017 年推出。**

但是，每一次科技革命都会带来困惑。在快速增长的环境中，很难公正地评估创新——更不用说估算它们的影响了。

启动了这场 AI 突破的 Transformer，已经成为了一个“*有争议的模型*”。目前存在两种极端观点：

1.  **热衷的采纳者：**他们在各个领域使用 Transformer，包括 NLP 之外的领域。即便他们不能或不想使用，仍然被雇主、经理、投资者等逼迫使用。

1.  **怀疑论者和技术拒绝者：**他们批评包括 Transformer 在内的 AI 模型。他们无法理解或接受通过增加数据量和层数来扩展模型，往往能超越基于严谨证明的优雅数学模型。

随着尘埃逐渐落定，现在是进行公正研究的时候了。

本文重点讨论用于预测的 Transformer 模型。我将讨论来自**学术界**、**工业界**和**领先研究人员**的最新发展。

我还将解释在什么情况下以及如何基于 Transformer 的预测…
