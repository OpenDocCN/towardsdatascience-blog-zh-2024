# 2024年KDD大会的因果AI——为什么那些现在不加入因果列车的公司，在2025年及以后将面临更大的竞争压力

> 原文：[https://towardsdatascience.com/causal-ai-at-kdd-2024-why-companies-that-wont-jump-on-the-causal-train-now-will-have-a-harder-bdd0671543cf?source=collection_archive---------5-----------------------#2024-09-30](https://towardsdatascience.com/causal-ai-at-kdd-2024-why-companies-that-wont-jump-on-the-causal-train-now-will-have-a-harder-bdd0671543cf?source=collection_archive---------5-----------------------#2024-09-30)

## 培养因果专业知识是一个过程，而不是一个事件

[](https://aleksander-molak.medium.com/?source=post_page---byline--bdd0671543cf--------------------------------)[![Aleksander Molak](../Images/7fca5018f6ce88297fae31cef1fe0e6c.png)](https://aleksander-molak.medium.com/?source=post_page---byline--bdd0671543cf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdd0671543cf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bdd0671543cf--------------------------------) [Aleksander Molak](https://aleksander-molak.medium.com/?source=post_page---byline--bdd0671543cf--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdd0671543cf--------------------------------) ·14分钟阅读·2024年9月30日

--

![](../Images/3f6e4cb96aaf81ab3f200ee2409b1128.png)

我在2024年KDD大会上，巴塞罗那

因果建模是一个总括性术语，涵盖了一系列方法，这些方法可以让我们建模我们行为对世界的影响。

因果模型与传统的机器学习模型在许多方面有所不同。

它们之间最重要的区别在于，训练传统机器学习模型所用的观测数据中所包含的信息——通常来说——不足以持续准确地建模我们行为的效果。

结果是什么？

使用传统的机器学习方法来建模我们行为的结果，原则上会导致偏颇的决策。

一个很好的例子是使用基于历史数据训练的回归模型进行营销组合建模。

另一个例子呢？

使用基于历史观测数据训练的XGBoost来预测流失的概率，并在预测的流失概率超过某个阈值时发送营销活动。
