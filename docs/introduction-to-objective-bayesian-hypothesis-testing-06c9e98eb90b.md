# 目标贝叶斯假设检验介绍

> 原文：[https://towardsdatascience.com/introduction-to-objective-bayesian-hypothesis-testing-06c9e98eb90b?source=collection_archive---------6-----------------------#2024-06-11](https://towardsdatascience.com/introduction-to-objective-bayesian-hypothesis-testing-06c9e98eb90b?source=collection_archive---------6-----------------------#2024-06-11)

## 如何利用默认贝叶斯因子推导假设的后验概率

[](https://medium.com/@ryan.burn?source=post_page---byline--06c9e98eb90b--------------------------------)[![Ryan Burn](../Images/5d45aa3c86bbcd5f30e92f2cb6a484f6.png)](https://medium.com/@ryan.burn?source=post_page---byline--06c9e98eb90b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--06c9e98eb90b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--06c9e98eb90b--------------------------------) [Ryan Burn](https://medium.com/@ryan.burn?source=post_page---byline--06c9e98eb90b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--06c9e98eb90b--------------------------------) ·15 分钟阅读·2024年6月11日

--

![](../Images/2fe67ce12a5a87609dc0f3c5bc28eed3.png)

卡拉马祖精神病医院——Cushny 和 Peebles 进行氟烯丙胺临床试验的地方 [14]

假设检验贯穿于统计学教育和科学研究，并且有充分的理由。实验通常是从假设出发，或者产生的数据导致产生一个假设。例如，考虑以下来自药理学家 Cushny 和 Peebles 的数据集[1]：

Cushny 和 Peebles 希望找到一种有效的催眠药。为此，他们在密歇根州卡拉马祖的疯人院给病人服用了不同相关药物的小剂量，并测量了睡眠活动。他们的数据集在某种程度上很有名[2]。这是学生[9]首次应用 t 分布的例子之一，后来由 Ronald Fisher 在他那本具有高度影响力的著作《研究工作者的统计方法 [15, p. 119]》中进行了分析。

看数据时，几个自然假设自然而然地出现：*L-氟烯丙胺 HBr* 在诱导睡眠方面是否比 *L-莨菪碱 HBr* 更有效？*L-莨菪碱 HBr* 是否比没有药物更好…
