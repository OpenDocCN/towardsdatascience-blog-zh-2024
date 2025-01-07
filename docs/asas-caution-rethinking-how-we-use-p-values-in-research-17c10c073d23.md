# ASA 的警示：重新思考我们在研究中如何使用 p 值

> 原文：[`towardsdatascience.com/asas-caution-rethinking-how-we-use-p-values-in-research-17c10c073d23?source=collection_archive---------16-----------------------#2024-06-11`](https://towardsdatascience.com/asas-caution-rethinking-how-we-use-p-values-in-research-17c10c073d23?source=collection_archive---------16-----------------------#2024-06-11)

## 理解 ASA 的声明，提升你的数据科学实践

[](https://medium.com/@shreef.nasser?source=post_page---byline--17c10c073d23--------------------------------)![Sheref Nasereldin 博士](https://medium.com/@shreef.nasser?source=post_page---byline--17c10c073d23--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17c10c073d23--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17c10c073d23--------------------------------) [Sheref Nasereldin 博士](https://medium.com/@shreef.nasser?source=post_page---byline--17c10c073d23--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17c10c073d23--------------------------------) ·8 分钟阅读·2024 年 6 月 11 日

--

![](img/5426f67726670924c7bfcfb537163932.png)

图片由 [Jason Dent](https://unsplash.com/@jdent?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言：

在数据科学领域，计算 p 值是一项非常常见的任务，是假设检验的核心。无论你是在分析 A/B 测试结果、进行医学研究，还是评估市场趋势，p 值都是解读数据的重要指标。然而，误解这一指标可能导致错误的结论。近七年前，**美国统计协会（ASA）**发布了关于最佳实践和常见误用的声明，提醒人们在解读 p 值时应避免的错误做法。尽管如此，自那时以来，这一指标的误用并未显著减少。

# 假设检验：

为了希望能够全面理解这一声明，我将首先列出成功进行假设检验所需的步骤：

+   写下**零假设（H0）**和**备择假设（H1）**。

+   为了保证结果的严谨性，决定显著性水平（𝛂）——这是犯第一类错误的概率，即当零假设 H0 为真时却错误地拒绝它。显著性水平通常设定为𝛂 = 0.05，但这仅仅是一个…
