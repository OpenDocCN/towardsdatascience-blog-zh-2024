# 探索LLM代理的世界：初学者指南

> 原文：[https://towardsdatascience.com/navigating-the-world-of-llm-agents-a-beginners-guide-3b8d499db7a9?source=collection_archive---------0-----------------------#2024-01-10](https://towardsdatascience.com/navigating-the-world-of-llm-agents-a-beginners-guide-3b8d499db7a9?source=collection_archive---------0-----------------------#2024-01-10)

## 一步一步指南，发现并利用LLM代理和工具包的力量

[](https://dmnkplzr.medium.com/?source=post_page---byline--3b8d499db7a9--------------------------------)[![Dominik Polzer](../Images/7e48cd15df31a0ab961391c0d57521de.png)](https://dmnkplzr.medium.com/?source=post_page---byline--3b8d499db7a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b8d499db7a9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3b8d499db7a9--------------------------------) [Dominik Polzer](https://dmnkplzr.medium.com/?source=post_page---byline--3b8d499db7a9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b8d499db7a9--------------------------------) ·19分钟阅读·2024年1月10日

--

![](../Images/aa0d864037570dcbf5a98fd8054fa1b7.png)

思维链提示——图片由作者提供

# 目录

[**简介**](#1336)

[什么是代理？](#8ac1)

[我们需要什么来构建自己的智能助手？](#f62c)

[我们如何连接所有必要的技能？—— 代理背后的理论](#1b20)

[代理是如何工作的？—— 思维链](#b4be)

[代理执行器——代理背后的代理](#1ea8)

[**从理论到实践**](#dd50)

[如何使用SQLDatabaseToolkit？](#b0ad)

[实践教程](#065e)

[**总结**](#2a8c)

# 简介

这篇文章讲述的是如何让LLM（大语言模型）独立解决复杂任务。

人类在不断吸收信息、做出决策、采取行动、观察变化，然后做出下一个决策的能力上非常出色。我们的一生就是一个永无止境的**观察、思考和行动的链条**。我们希望将这一概念应用于LLM，使其能够持续做出新的决策，从而逐步接近复杂问题的解决方案。
