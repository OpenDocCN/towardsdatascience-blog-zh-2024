# 从零开始构建蛋白质的 Transformer 模型

> 原文：[`towardsdatascience.com/building-transformer-models-for-proteins-from-scratch-60884eab5cc8?source=collection_archive---------4-----------------------#2024-05-07`](https://towardsdatascience.com/building-transformer-models-for-proteins-from-scratch-60884eab5cc8?source=collection_archive---------4-----------------------#2024-05-07)

## 构建和评估蛋白质语言模型的实用指南

[](https://medium.com/@yuan_tian?source=post_page---byline--60884eab5cc8--------------------------------)![Yuan Tian](https://medium.com/@yuan_tian?source=post_page---byline--60884eab5cc8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60884eab5cc8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60884eab5cc8--------------------------------) [袁天](https://medium.com/@yuan_tian?source=post_page---byline--60884eab5cc8--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60884eab5cc8--------------------------------) ·13 分钟阅读·2024 年 5 月 7 日

--

# 引言

在我们对蛋白质科学基础理解的基础上，参考[**上一篇文章**](https://medium.com/@yuan_tian/a-primer-to-protein-science-1b6778ae995e)，我们现在准备探索 AI/ML 与蛋白质科学的激动人心的交汇点。本文重点讨论基于 Transformer 的语言模型，这一技术支撑了像 ChatGPT 这样的高级聊天机器人。我不会在这里花费太多篇幅详细讲解 Transformer。对于这一点，我强烈推荐 Jay Alammar 的博客文章“[The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)”，该文章提供了详细的解析和精美的插图。相反，我们将重点介绍 Transformer 在蛋白质分析中的实际应用。

具体来说，我们将构建一个基础的蛋白质 Transformer 模型，预测抗体序列的抗原特异性。这个项目将增强我们对 Transformer 实现的理解，并探索它们在蛋白质科学中的潜在应用。

Transformer 是在开创性论文“[Attention Is All You Need](https://arxiv.org/abs/1706.03762)”中提出的神经网络模型，具有编码器和解码器组件。像 BERT（双向编码表示 Transformer）这样的模型利用编码器来理解语言，并在下游任务（如分类）中表现出色。在这里，我们将实现并训练一个基于编码器的模型，以将抗体分类为 HIV-1 或 SARS-CoV-2 特异性（**图 1**）。
