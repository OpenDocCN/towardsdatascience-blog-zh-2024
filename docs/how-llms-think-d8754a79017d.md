# LLM如何思考

> 原文：[https://towardsdatascience.com/how-llms-think-d8754a79017d?source=collection_archive---------1-----------------------#2024-06-07](https://towardsdatascience.com/how-llms-think-d8754a79017d?source=collection_archive---------1-----------------------#2024-06-07)

## 研究论文概览：“扩展单义性：从Claude 3 Sonnet中提取可解释特征”

[](https://medium.com/@cristianleo120?source=post_page---byline--d8754a79017d--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--d8754a79017d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d8754a79017d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d8754a79017d--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--d8754a79017d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d8754a79017d--------------------------------) ·阅读时间10分钟·2024年6月7日

--

![](../Images/7585d40f377e70c9ece698450f690525.png)

图片由DALL-E生成

你是否曾想过AI模型是如何“思考”的？试想一下，能够窥视机器的大脑，看着它的齿轮转动。这正是Anthropic的一篇突破性论文所探讨的内容。论文标题为《[扩展单义性：从Claude 3 Sonnet中提取可解释特征](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)》，研究深入了解并解释AI的思维过程。

研究人员成功从Claude 3 Sonnet模型中提取出一些特征，展示了该模型在思考关于名人、城市，甚至软件安全漏洞时的表现。这就像是窥视AI的大脑，揭示了它理解并用于做决策的概念。

## 研究论文概述

在这篇论文中，Anthropic团队，包括Adly Templeton、Tom Conerly、Jonathan Marcus等人，旨在让AI模型更加透明。他们专注于Claude 3 Sonnet，一个中型AI模型，目标是扩展单义性——本质上是确保模型中的每个特征都有明确的、单一的含义。

但为什么扩展单义性如此重要？单义性究竟是什么意思？我们很快就会深入探讨这个问题。
