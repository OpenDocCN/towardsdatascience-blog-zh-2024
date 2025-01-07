# 我曾经讨厌过拟合，但现在我已经彻底理解它

> 原文：[https://towardsdatascience.com/i-used-to-hate-overfitting-but-now-im-grokking-it-e6e1dcfbdfd8?source=collection_archive---------3-----------------------#2024-07-23](https://towardsdatascience.com/i-used-to-hate-overfitting-but-now-im-grokking-it-e6e1dcfbdfd8?source=collection_archive---------3-----------------------#2024-07-23)

## 超越过拟合的惊人泛化

[](https://blog.heilmela.eu/?source=post_page---byline--e6e1dcfbdfd8--------------------------------)[![Laurin Heilmeyer](../Images/bf503815c37deba7a84ae313530f20e6.png)](https://blog.heilmela.eu/?source=post_page---byline--e6e1dcfbdfd8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e6e1dcfbdfd8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e6e1dcfbdfd8--------------------------------) [Laurin Heilmeyer](https://blog.heilmela.eu/?source=post_page---byline--e6e1dcfbdfd8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e6e1dcfbdfd8--------------------------------) ·阅读时长：8分钟·2024年7月23日

--

![](../Images/37cabc7cc6b5c1869d4806c226047caf.png)

图片由[DALL-E](https://openai.com/index/dall-e-2/)提供

作为一个曾经花费大量时间研究各种计算机科学话题的人，其中数学抽象有时非常枯燥和抽象，我发现数据科学的实践性和动手操作性给人一种焕然一新的感觉。它总是让我惊讶，甚至最简单的想法也能带来令人着迷的结果。

本文讲述了我最近偶然发现的其中一个令人惊讶的启示。

## 一个令人沮丧的开始

我永远不会忘记我的本科论文的实施过程。虽然它与机器学习无关，但它对我产生了深远的影响，每当我在调试神经网络时，我都会时常提醒自己。那段时间非常紧张，因为论文是关于一种声音传播的分析模型，目标是在浏览器中运行，因此性能非常有限，导致长时间运行的模拟。它经常在运行几个小时后无法完成。但最糟糕的体验是解读错误配置的模拟结果，那些令人困惑的结果常常让我认为整个模型毫无意义。

当我亲自训练神经网络时，时不时也会发生同样的情况。它可能会令人感到疲惫...
