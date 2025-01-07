# 使用离线强化学习试验在线平台干预

> 原文：[https://towardsdatascience.com/using-offline-reinforcement-learning-to-trial-online-platform-interventions-b72ab22f71c0?source=collection_archive---------7-----------------------#2024-11-04](https://towardsdatascience.com/using-offline-reinforcement-learning-to-trial-online-platform-interventions-b72ab22f71c0?source=collection_archive---------7-----------------------#2024-11-04)

## 离线强化学习与仿真用于策划在线平台的用户参与。

[](https://medium.com/@danielmillerAI?source=post_page---byline--b72ab22f71c0--------------------------------)[![Daniel Miller](../Images/9728d4c65817946169e66b228032980c.png)](https://medium.com/@danielmillerAI?source=post_page---byline--b72ab22f71c0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b72ab22f71c0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b72ab22f71c0--------------------------------) [Daniel Miller](https://medium.com/@danielmillerAI?source=post_page---byline--b72ab22f71c0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b72ab22f71c0--------------------------------) ·10分钟阅读·2024年11月4日

--

![](../Images/2755bdde309595ca69cc598166538741.png)

图片来源：[Fernando Freitas](https://unsplash.com/@_fernandofs_?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于 [Unsplash](https://unsplash.com/photos/a-red-and-white-logo-AcUdFVUGJYM?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## **概述**

本文基于我们在[使用深度学习预测在线平台的用户参与度的研究](/deep-learning-to-predict-engagement-in-online-platforms-dfd79515b85e)进行扩展。我们发现，预测用户行为依赖于足够的历史数据，这与现有的相关研究一致。

非付费平台通常通过徽章、奖章和在线奖励来鼓励和奖励用户参与。虽然这些措施可能有效，但往往会产生意想不到的行为和混合的结果。例如，[Coursera](https://arxiv.org/pdf/1403.3100)和[StackOverflow](https://www.researchgate.net/publication/343689654_One_Size_Does_Not_Fit_All_A_Study_of_Badge_Behavior_in_Stack_Overflow)都曾观察到“引导效应”。用户常常为了获得徽章而参与，之后则会脱离平台。尽管这在短期内提高了参与度，但它只是一个有限的策略，无法将少数用户转化为长期参与者。

![](../Images/63cba0616324eb6a35c6a7fd898a36d4.png)

在线激励的引导效应示例。参考自Yanovsky、Hoernle与[Gal对StackOverflow中激励寻求的研究](https://arxiv.org/pdf/2008.06125)。

尝试激励措施也可能带来风险。[一个名为 Old Weather 的 Zooniverse 项目](https://discovery.ucl.ac.uk/id/eprint/1412171/1/p79-eveleigh.pdf)试验了一种竞争排名策略，结果适得其反。
