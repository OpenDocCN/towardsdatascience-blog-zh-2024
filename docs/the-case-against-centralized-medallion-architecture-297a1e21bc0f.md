# 反对集中式奖章架构的案例

> 原文：[`towardsdatascience.com/the-case-against-centralized-medallion-architecture-297a1e21bc0f?source=collection_archive---------1-----------------------#2024-12-09`](https://towardsdatascience.com/the-case-against-centralized-medallion-architecture-297a1e21bc0f?source=collection_archive---------1-----------------------#2024-12-09)

## 为什么定制化、去中心化的数据质量优于奖章架构

[](https://medium.com/@bernd.wessely?source=post_page---byline--297a1e21bc0f--------------------------------)![Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--297a1e21bc0f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--297a1e21bc0f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--297a1e21bc0f--------------------------------) [Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--297a1e21bc0f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--297a1e21bc0f--------------------------------) ·8 分钟阅读·2024 年 12 月 9 日

--

![](img/7a4abac7cb49b855a562c05a3584e8bd.png)

DALL-E 生成

我见过太多文章赞扬奖章架构是解决企业数据质量问题的最佳方案。乍一看，这种结构化的三层方法听起来是显而易见的——将数据组织成简洁的铜层、银层和金层，便建立了一个完美的数据质量提升方案。

但仔细观察后，我对这种架构方法的反感愈发强烈。的确，它承诺提供一致的、可扩展的和集中的信息质量提升。然而，在实际操作中，质量问题总是被过晚且僵化地用相同的工具进行修正，无论其背景如何。

企业是复杂的自适应系统，拥有截然不同的数据源，每个数据源在信息质量方面面临独特的挑战。为什么要对它们强制实行相同的僵化流程呢？将所有数据源强行纳入相同的集中的质量框架只会导致低效和不必要的开销。

我想挑战奖章架构作为企业数据质量问题的最佳解决方案。我将为一种更定制化、去中心化的方法提出理由——这一方法灵感来源于全面质量管理（TQM），并与之相一致……
