# 5 次点击惊艳全场：如何通过更改数据类型快速优化 Power BI 模型

> 原文：[`towardsdatascience.com/5-clicks-to-wow-how-changing-data-types-can-quickly-optimize-your-power-bi-model-ab661b96b4fb?source=collection_archive---------7-----------------------#2024-10-04`](https://towardsdatascience.com/5-clicks-to-wow-how-changing-data-types-can-quickly-optimize-your-power-bi-model-ab661b96b4fb?source=collection_archive---------7-----------------------#2024-10-04)

## 优化 Power BI 语义模型不一定总是一个令人生畏且耗时的任务。你通常可以获得许多快速且轻松的胜利！

[](https://datamozart.medium.com/?source=post_page---byline--ab661b96b4fb--------------------------------)![Nikola Ilic](https://datamozart.medium.com/?source=post_page---byline--ab661b96b4fb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ab661b96b4fb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ab661b96b4fb--------------------------------) [Nikola Ilic](https://datamozart.medium.com/?source=post_page---byline--ab661b96b4fb--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ab661b96b4fb--------------------------------) ·阅读时间 6 分钟·2024 年 10 月 4 日

--

![](img/f854d30bc229cdc0a3828de79a4045da.png)

图片由作者提供

几周前，我被要求优化一个运行缓慢的 Power BI 报告。当然，Power BI 报告运行缓慢的原因可能有很多，但在这篇文章中，我想与大家分享一些“低悬果实”，以及如何通过应用一些非常简单的优化技术，显著提升 Power BI 语义模型的性能。

# 设定场景

为了演示，我将使用一个事实表，该表包含了虚构公司“Customer First”的客户支持部门进行的聊天数据。这个表大约有 900 万行，在 Power BI 和分析工作负载的上下文中，这并不算是一个大的表。为了简便起见，我们假设我们的模型仅由这一张表组成。最后，语义模型被配置为导入模式模型。如果你想了解 Power BI 中数据是如何存储的，我建议你首先阅读[这篇文章](https://data-mozart.com/vertipaq-brain-muscles-behind-power-bi/)。
