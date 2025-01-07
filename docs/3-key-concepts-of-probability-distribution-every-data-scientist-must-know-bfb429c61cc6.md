# 每个数据科学家必须了解的概率分布的3个关键概念

> 原文：[https://towardsdatascience.com/3-key-concepts-of-probability-distribution-every-data-scientist-must-know-bfb429c61cc6?source=collection_archive---------6-----------------------#2024-05-28](https://towardsdatascience.com/3-key-concepts-of-probability-distribution-every-data-scientist-must-know-bfb429c61cc6?source=collection_archive---------6-----------------------#2024-05-28)

## 你知道PMF、PDF和CDF实际上是什么意思吗？它们是所有分布的核心。

[](https://zubairhossain.medium.com/?source=post_page---byline--bfb429c61cc6--------------------------------)[![Md. Zubair](../Images/1b983a23226ce7561796fa5b28c00d65.png)](https://zubairhossain.medium.com/?source=post_page---byline--bfb429c61cc6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bfb429c61cc6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bfb429c61cc6--------------------------------) [Md. Zubair](https://zubairhossain.medium.com/?source=post_page---byline--bfb429c61cc6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bfb429c61cc6--------------------------------) ·5分钟阅读·2024年5月28日

--

![](../Images/907583d774dc179ccf3fa1d2a6e0f050.png)

图片由作者提供

## 动机

有许多实际事件，我们知道其确切的结果，例如1+1=2，3x2=6，等等。还有许多不可能发生的事件，例如太阳从西方升起、牛在天空中飞等。在这两种情况下，我们对结果有100%的把握。但对于不可预测的事件，例如明天早上是否会下雨，我们能有多大的把握呢？在这里，概率的必要性就体现出来了！

`***[注意：*** [***如果你想执行包含的分布图，请访问笔记本***](https://deepnote.com/workspace/zubair-6f469541-5170-4b0d-a125-36e6a5c03769/project/Article-code-c3041ae8-e1f6-4485-999e-5738394b76e3/notebook/Probability%20PDF%20and%20CDF-c6c26d65d9e74be38914ecd8f55fcee3)***.]***`

## 目录

1.  `[概率](#bc3c)`

1.  `[概率分布](#6b11)`

1.  `[离散概率分布和PMF](#76ae)`

1.  `[连续概率分布、PDF和CDF](#f327)`

1.  `[总结](#dc62)`

## 概率

***概率表示随机事件发生的可能性。*** 以抛硬币为例，如果你随机抛掷硬币，可能有两种结果——正面或反面。因此，如果抛掷硬币，结果是正面的概率为50%，而…
