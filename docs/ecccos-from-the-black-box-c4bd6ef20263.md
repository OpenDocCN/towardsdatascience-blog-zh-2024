# ECCCos 来自黑箱

> 原文：[`towardsdatascience.com/ecccos-from-the-black-box-c4bd6ef20263?source=collection_archive---------4-----------------------#2024-02-08`](https://towardsdatascience.com/ecccos-from-the-black-box-c4bd6ef20263?source=collection_archive---------4-----------------------#2024-02-08)

## 通过能量约束的符合性反事实实现忠实的模型解释

[](https://medium.com/@patrick.altmeyer?source=post_page---byline--c4bd6ef20263--------------------------------)![Patrick Altmeyer](https://medium.com/@patrick.altmeyer?source=post_page---byline--c4bd6ef20263--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c4bd6ef20263--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4bd6ef20263--------------------------------) [Patrick Altmeyer](https://medium.com/@patrick.altmeyer?source=post_page---byline--c4bd6ef20263--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4bd6ef20263--------------------------------) ·15 分钟阅读·2024 年 2 月 8 日

--

反事实解释提供了一种直观且简洁的方式来解释不透明的机器学习（ML）模型。它们的工作原理是在扰动输入的前提下，实现预测输出的期望变化。

> 如果你之前没有听说过反事实解释，可以查看我的入门文章：1) [黑箱模型的个体回溯](https://medium.com/towards-data-science/individual-recourse-for-black-box-models-5e9ed1e4b4cc) 和 2) [可解释人工智能的新工具](https://medium.com/towards-data-science/a-new-tool-for-explainable-ai-65834e757c28)。

通常，有许多方式可以实现这一目标，换句话说，许多不同的反事实可能会产生相同的期望结果。因此，研究人员面临的一个关键挑战是，首先，定义反事实解释的某些期望特征，其次，提出实现这些特征的高效方法。

反事实解释最重要且研究最深入的特征之一是“可 plausibility”：解释应该看起来对人类来说是现实的。可 plausibility 与可操作性、鲁棒性（Artelt 等人，2021 年）和因果有效性（Mahajan、Tan 和 Sharma，2020 年）呈正相关。为了实现 plausibility，许多现有方法依赖于替代模型。这种方法直接，但它也使事情更加复杂：本质上，它将学习数据的合理解释的任务从模型本身转移到替代模型上。
