# Pearson 与 Spearman 相关性：在变量之间找到和谐

> 原文：[`towardsdatascience.com/pearson-vs-spearman-correlation-find-harmony-between-the-variables-08e201ca9f7f?source=collection_archive---------6-----------------------#2024-01-18`](https://towardsdatascience.com/pearson-vs-spearman-correlation-find-harmony-between-the-variables-08e201ca9f7f?source=collection_archive---------6-----------------------#2024-01-18)

## 你应该为你的任务使用哪种相关性度量？了解你需要知道的所有关于 Pearson 和 Spearman 相关性的知识

[](https://medium.com/@riccardo.andreoni?source=post_page---byline--08e201ca9f7f--------------------------------)![Riccardo Andreoni](https://medium.com/@riccardo.andreoni?source=post_page---byline--08e201ca9f7f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--08e201ca9f7f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--08e201ca9f7f--------------------------------) [Riccardo Andreoni](https://medium.com/@riccardo.andreoni?source=post_page---byline--08e201ca9f7f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--08e201ca9f7f--------------------------------) ·阅读时间：7 分钟·2024 年 1 月 18 日

--

想象一个**交响乐团**在演出前调音。每个音乐家都调整他们的音符，以与其他人和谐地融合，确保**无缝的音乐体验**。在**数据科学**中，数据集中的变量可以与**乐团的音乐家**相比较：理解它们之间的和谐或不和谐至关重要。

![](img/2cd39090d9590cac52a499fb4df60505.png)

[图片来源：pixabay.com.](https://en.wikipedia.org/wiki/Spearman%27s_rank_correlation_coefficient)

**相关性**是一个统计度量，像是乐团的指挥，指导我们理解数据中**复杂的关系**。在这里，我们将重点讨论两种相关性：[**Pearson**](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)和[**Spearman**](https://en.wikipedia.org/wiki/Spearman%27s_rank_correlation_coefficient)。

如果我们的数据是一个交响乐，Pearson 和 Spearman 就是我们的**乐团指挥**：他们有着独特的风格来诠释这场交响乐，各自有着不同的优势和细微之处。理解这两种不同的方法将帮助你**提取洞察**并**理解变量之间的联系**。

# Pearson 相关性

**Pearson 相关系数**，用*r*表示，量化了两个变量之间**线性关系**的强度和方向…
