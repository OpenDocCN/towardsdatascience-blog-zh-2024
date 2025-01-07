# 使用信用卡欺诈数据的模型可解释性

> 原文：[https://towardsdatascience.com/model-interpretability-using-credit-card-fraud-data-f219ff7ec89d?source=collection_archive---------4-----------------------#2024-06-12](https://towardsdatascience.com/model-interpretability-using-credit-card-fraud-data-f219ff7ec89d?source=collection_archive---------4-----------------------#2024-06-12)

## 为什么模型可解释性很重要

[](https://dan-to.medium.com/?source=post_page---byline--f219ff7ec89d--------------------------------)[![Danila Morozovskii](../Images/d53b987de52b8c2d4ce264b142b05950.png)](https://dan-to.medium.com/?source=post_page---byline--f219ff7ec89d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f219ff7ec89d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f219ff7ec89d--------------------------------) [Danila Morozovskii](https://dan-to.medium.com/?source=post_page---byline--f219ff7ec89d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f219ff7ec89d--------------------------------) ·17分钟阅读·2024年6月12日

--

最近，我偶然发现了一本在线书籍，书中描述了可用于机器学习模型可解释性的不同工具（[https://christophm.github.io/interpretable-ml-book/](https://christophm.github.io/interpretable-ml-book/)）。机器学习模型不应该是黑箱，并且可以解释的这一想法让我非常着迷，我决定深入研究这个话题。之前，当我开始进行一个新的机器学习项目时，我会遵循相同的程序：识别问题、熟悉数据集、特征工程、选择模型、训练/测试和超参数调优，以及结果分析。然而，我没有意识到自己漏掉了最关键的一步：模型可解释性。

## 什么是模型可解释性？

模型可解释性是解释黑箱（模型）如何工作以及如何做出预测的过程。让我们假设一个情况，一个人申请了信用贷款并被拒绝，因为模型做出了负面预测。任何人都会想知道为什么被拒绝，以及他们可能需要改变什么，才能使决定变得积极，而银行员工只能指着机器学习模型说“它是这么说的！”。这种情况并不好，并且会损害银行的声誉，因为看起来银行对其产品没有控制权。如果银行员工能够向客户解释哪些特定特征在做出预测时发挥了作用，那就会好得多。
