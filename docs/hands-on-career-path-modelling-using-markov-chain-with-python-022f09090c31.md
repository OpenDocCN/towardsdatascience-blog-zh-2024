# 使用马尔可夫链进行职业路径建模，配合 Python 实践

> 原文：[`towardsdatascience.com/hands-on-career-path-modelling-using-markov-chain-with-python-022f09090c31?source=collection_archive---------11-----------------------#2024-07-29`](https://towardsdatascience.com/hands-on-career-path-modelling-using-markov-chain-with-python-022f09090c31?source=collection_archive---------11-----------------------#2024-07-29)

## 这就是我如何使用基础概率模拟职业发展的方式

[](https://piero-paialunga.medium.com/?source=post_page---byline--022f09090c31--------------------------------)![Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--022f09090c31--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--022f09090c31--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--022f09090c31--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--022f09090c31--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--022f09090c31--------------------------------) ·阅读时间 12 分钟·2024 年 7 月 29 日

--

![](img/b04ebfde370bda3adfad906991fa4615.png)

图像由作者使用 DALL·E 生成

从专业角度来说，我是一个非常奇怪的人：我在一家初创公司担任**软件/机器学习工程师**，我拥有**物理学**硕士学位，并且即将为我的**航空航天与机械工程**博士论文进行答辩。在我不断变化的职业生涯中，有两件事始终不变：我对科学的热爱和对编程的激情。

将科学与编程结合的一个美妙方式就是进行**建模**。我的意思是，为了描述世界，你基于对现实的某种程度的近似做出合理的假设。基于这个假设和初始的近似，我们可以**模拟一个给定的过程**。这个模拟将给我们一些结果，这些结果来源于最初的假设，但在模拟之前是无法精确预测的。

举个例子，假设我们正在试图弄清楚多少头牛可以放进一个围栏。一个物理学家可能会做出一个相当离奇的假设：

> “让我们考虑一头正方形的牛”

这意味着我们**近似**将一头牛的形状看作一个正方形。然后我们将围栏近似为一个更大的正方形……
