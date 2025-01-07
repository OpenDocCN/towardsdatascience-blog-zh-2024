# LLM代理——直观且详尽地解释

> 原文：[https://towardsdatascience.com/llm-agents-intuitively-and-exhaustively-explained-8905858e18e2?source=collection_archive---------2-----------------------#2024-01-05](https://towardsdatascience.com/llm-agents-intuitively-and-exhaustively-explained-8905858e18e2?source=collection_archive---------2-----------------------#2024-01-05)

## 语言建模 | 自主系统 | 人工智能

## 赋能语言模型进行推理与行动

[](https://medium.com/@danielwarfield1?source=post_page---byline--8905858e18e2--------------------------------)[![Daniel Warfield](../Images/c1c8b4dd514f6813e08e401401324bca.png)](https://medium.com/@danielwarfield1?source=post_page---byline--8905858e18e2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8905858e18e2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8905858e18e2--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--8905858e18e2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8905858e18e2--------------------------------) ·阅读时间37分钟·2024年1月5日

--

![](../Images/f550ca30868bdce8770c23562577c043.png)

“决策引导者”由丹尼尔·沃菲尔德（Daniel Warfield）使用MidJourney创作。所有图像均由作者提供，除非另有说明

本文聚焦于“代理”这一概念，它允许语言模型进行推理并与世界互动。首先，我们将讨论什么是代理以及为什么它们很重要；接着，我们将通过几种代理的形式来建立对其工作原理的直观理解；然后，我们将通过实现两种代理来探索代理在实际环境中的应用，一种使用LangChain实现，另一种用Python从零开始实现。

在本文结束时，您将理解代理如何赋能语言模型执行复杂任务，您也将了解如何自己构建一个代理。

**这对谁有用？** 任何对构建前沿语言建模系统所需工具感兴趣的人。

**这篇文章的难度如何？** 这篇文章在概念上简单，但包含了过去一年中的前沿研究，因此对各个经验层次的数据科学家都具有相关性。

**前提条件：** 无，尽管对语言模型（如OpenAI的GPT）有一些基本了解可能会有所帮助。如果您对某些内容感到困惑，我在本文末尾附加了一些相关资料，供您参考……
