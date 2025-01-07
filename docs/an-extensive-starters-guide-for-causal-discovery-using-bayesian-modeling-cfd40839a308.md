# 贝叶斯建模用于因果发现的详尽入门指南

> 原文：[https://towardsdatascience.com/an-extensive-starters-guide-for-causal-discovery-using-bayesian-modeling-cfd40839a308?source=collection_archive---------1-----------------------#2024-10-19](https://towardsdatascience.com/an-extensive-starters-guide-for-causal-discovery-using-bayesian-modeling-cfd40839a308?source=collection_archive---------1-----------------------#2024-10-19)

## 贝叶斯方法正在变得越来越流行，但刚开始时可能会让人不知所措。本篇详尽的指南将引导你了解因果发现方法的应用、库和依赖项。

[](https://erdogant.medium.com/?source=post_page---byline--cfd40839a308--------------------------------)[![Erdogan Taskesen](../Images/f6b52ed5acb60bb16d42257a3a1232de.png)](https://erdogant.medium.com/?source=post_page---byline--cfd40839a308--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfd40839a308--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cfd40839a308--------------------------------) [Erdogan Taskesen](https://erdogant.medium.com/?source=post_page---byline--cfd40839a308--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfd40839a308--------------------------------) ·35 分钟阅读·2024年10月19日

--

![](../Images/0b05649a8b03710dec65d64aba5e9e08.png)

无监督因果发现的全景。图片由作者提供。

贝叶斯技术的无限可能性也是其弱点；应用范围极广，理解这些技术与不同解决方案的关系以及应用的方式可能会很麻烦。在我之前的博客中，我写过关于结构学习、参数学习、推断以及不同贝叶斯库的比较概述等多个主题。**在这篇博客中，我将引导你了解贝叶斯应用的全景，并描述不同的因果发现方法是如何与应用相匹配的。** 换句话说，*如何使用离散或连续数据集创建因果网络（有向无环图）？* *你能否在有（无）反应/处理变量的情况下确定因果网络？* *你如何决定使用哪些搜索方法，如PC、爬山搜索等？* **阅读完这篇博客后，你将知道从哪里开始，并且能够根据你的使用案例选择最合适的贝叶斯因果发现技术。** *慢慢来，找些时间，抓住一杯*…
