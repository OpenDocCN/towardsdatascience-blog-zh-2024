# 持续学习的指标

> 原文：[https://towardsdatascience.com/the-metrics-of-continual-learning-08f2d1cd959b?source=collection_archive---------7-----------------------#2024-11-18](https://towardsdatascience.com/the-metrics-of-continual-learning-08f2d1cd959b?source=collection_archive---------7-----------------------#2024-11-18)

## 这三种指标是常用的

[](https://pascaljanetzky.medium.com/?source=post_page---byline--08f2d1cd959b--------------------------------)[![Pascal Janetzky](../Images/43d68509b63c5f9b3fc9cef3cbfc1a88.png)](https://pascaljanetzky.medium.com/?source=post_page---byline--08f2d1cd959b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--08f2d1cd959b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--08f2d1cd959b--------------------------------) [Pascal Janetzky](https://pascaljanetzky.medium.com/?source=post_page---byline--08f2d1cd959b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--08f2d1cd959b--------------------------------) ·4分钟阅读·2024年11月18日

--

持续学习是机器学习的一个子领域，处理的是在不断到来的数据上增量训练神经网络的问题。关键在于，数据不能完全存储，且往往不能从旧任务中携带任何样本。由于网络仅在当前可用的数据上进行优化，它们会覆盖旧的参数。在覆盖这些参数时，旧的知识通常会被破坏，也就是*遗忘*。

![](../Images/d654e321d3c7cdaa4d036a2843b03d45.png)

图片来源：[Reid Zura](https://unsplash.com/@reidzura?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

为了基准化持续学习和灾难性遗忘，持续学习研究中使用了几种评估指标。在本文中，我将详细介绍三种最常用的指标。虽然我将使用分类作为示例，但这些指标同样适用于其他问题，例如回归。如果你对持续学习这个主题不熟悉，我建议你阅读我之前的[两篇](/continual-learning-a-primer-e328ed1d072f) [文章](/continual-learning-the-three-common-scenarios-e6c3260fe0cb)，以便更深入理解该主题。正如我以前做过的那样，本文结尾我还会提供一些阅读推荐，帮助你进一步探索这一话题。

## 平均准确率

第一个常用的指标是平均准确率，通常缩写为 ACC。顾名思义，它衡量每个任务的（测试集）准确率，然后计算各个任务特定准确率的平均值。形式上是……
