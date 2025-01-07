# 倾向得分匹配是因果推断的基石

> 原文：[`towardsdatascience.com/propensity-score-matching-is-the-bedrock-of-causal-inference-b64d4a9c2606?source=collection_archive---------4-----------------------#2024-12-22`](https://towardsdatascience.com/propensity-score-matching-is-the-bedrock-of-causal-inference-b64d4a9c2606?source=collection_archive---------4-----------------------#2024-12-22)

## 意见

## 以及如何使用 Python 开始它

[](https://arijoury.medium.com/?source=post_page---byline--b64d4a9c2606--------------------------------)![Ari Joury 博士](https://arijoury.medium.com/?source=post_page---byline--b64d4a9c2606--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b64d4a9c2606--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b64d4a9c2606--------------------------------) [Ari Joury 博士](https://arijoury.medium.com/?source=post_page---byline--b64d4a9c2606--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b64d4a9c2606--------------------------------) ·阅读时长 7 分钟·2024 年 12 月 22 日

--

![](img/b6985266bd6f75e45d3275f841346dcb.png)

培训项目能否带来更多的经济繁荣？图像由 Leonardo AI 生成

当代数据科学大多回答了“发生了什么？”这个问题。例如，在我的公司，我们常常试图发现一家公司表现如何，以及如何通过相关性将一个表现指标与另一个表现指标联系起来。

一个更有力量的问题是“为什么会发生这种情况？”例如，如果我们检测到女性在管理层中的存在与公司收入之间存在显著的相关性，那么这里的因果关系是什么？或者，如果人们参加了培训项目，这会导致他们的表现提高吗？还是表现更好的人更愿意参加培训项目，从而我们只看到由于选择偏差所导致的效果？

[有几种方法](https://wangari.substack.com/p/understanding-the-why-10-techniques)可以揭示数据科学中的因果关系。倾向得分匹配（PSM）是其中一种较为传统的方法，它大约在[40 年前](https://academic.oup.com/aje/article/191/10/1671/6618833)出现。像[结构方程模型](https://ftp.cs.ucla.edu/pub/stat_ser/r370.pdf?utm_source=chatgpt.com)这样的其他方法也在同一时期出现。像[工具变量](https://scholar.harvard.edu/files/stock/files/tariff_appendixb.pdf)这样的方法则早在几代人之前就已经出现了。因果统计学仍然是一个非常活跃的领域，许多新方法正在不断开发。

PSM 的一个主要优势是它允许研究人员使用真实世界的数据。特别是，它…
