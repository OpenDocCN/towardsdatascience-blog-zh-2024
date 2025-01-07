# 评估机器学习中的训练-测试集划分策略：超越基础

> 原文：[https://towardsdatascience.com/evaluating-train-test-split-strategies-in-machine-learning-beyond-the-basics-c3e84b58ddce?source=collection_archive---------9-----------------------#2024-09-30](https://towardsdatascience.com/evaluating-train-test-split-strategies-in-machine-learning-beyond-the-basics-c3e84b58ddce?source=collection_archive---------9-----------------------#2024-09-30)

## 创建合适的测试集并安然入睡。

[](https://federicorucci.medium.com/?source=post_page---byline--c3e84b58ddce--------------------------------)[![Federico Rucci](../Images/60be2b0b9f72e330acf2cabbc373c82a.png)](https://federicorucci.medium.com/?source=post_page---byline--c3e84b58ddce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c3e84b58ddce--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c3e84b58ddce--------------------------------) [Federico Rucci](https://federicorucci.medium.com/?source=post_page---byline--c3e84b58ddce--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c3e84b58ddce--------------------------------) ·阅读时间：5分钟·2024年9月30日

--

在这篇文章中，我想探讨一个常常被提问者和回答者忽视的问题：“如何将数据集划分为训练集和测试集？”

在处理监督学习问题时，通常做法是将数据集划分为（至少）两部分：训练集和测试集。训练集用于研究现象，而测试集则用于验证学习到的信息是否能够在“未知”数据上进行复制，也就是说，验证数据是否能够应用于在前阶段没有出现过的数据。

许多人通常采用标准、显而易见的方法来做出这一决策。常见且不那么令人兴奋的回答是：“我随机划分可用数据，将20%到30%保留为测试集。”

那些深入研究的人会添加分层随机抽样的概念：即在保持一个或多个变量的固定比例的同时进行随机抽样。假设我们处在一个二分类的情境中，且目标变量的先验概率为5%。在目标变量上进行分层随机抽样意味着获取一个训练集和一个测试集，它们在目标变量的先验比例上保持5%的比例。

这种推理有时是必要的，例如在非常不平衡的分类问题中，但它们并不会为…
