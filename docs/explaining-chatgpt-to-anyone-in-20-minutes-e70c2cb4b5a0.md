# 在 <20 分钟内向任何人解释 ChatGPT

> 原文：[`towardsdatascience.com/explaining-chatgpt-to-anyone-in-20-minutes-e70c2cb4b5a0?source=collection_archive---------11-----------------------#2024-03-14`](https://towardsdatascience.com/explaining-chatgpt-to-anyone-in-20-minutes-e70c2cb4b5a0?source=collection_archive---------11-----------------------#2024-03-14)

## 将生成型大语言模型的核心组成部分提炼成一个易于理解的框架…

[](https://wolfecameron.medium.com/?source=post_page---byline--e70c2cb4b5a0--------------------------------)![Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--e70c2cb4b5a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e70c2cb4b5a0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e70c2cb4b5a0--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--e70c2cb4b5a0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e70c2cb4b5a0--------------------------------) ·阅读时间 14 分钟·2024 年 3 月 14 日

--

![](img/9a470d5ce35da59c990d833660858b2f.png)

（照片由 [Possessed Photography](https://unsplash.com/@possessedphotography?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/closeup-photo-of-white-robot-arm-jIBMSMs4_kA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

在过去的几年里，我们见证了生成型大语言模型（LLMs）的迅速发展，最终催生了前所未有的工具，如 ChatGPT。生成型 AI 现在已成为研究人员和公众中热门的话题。现在，比以往任何时候都更加重要的是，研究人员和工程师（即那些*构建*技术的人）必须具备将其创作的细节与他人沟通的能力。如果未能以易于理解和易于接触的方式传达 AI 的技术细节，可能会导致公众普遍的怀疑（例如，关于核能的研究曾走上过一条[类似的道路](https://en.wikipedia.org/wiki/Nuclear_power_debate)）或通过制定过于严苛的立法，阻碍我们领域的前进。在这篇概述中，我们将迈出小小的一步，通过提出并概述一个简单的三部分框架，帮助理解和解释生成型大语言模型。

**演示资源。** 这篇文章的灵感来源于我最近为 O'Reilly 做的关于大语言模型（LLMs）基础知识的演讲。这次演讲的目标是提供一个“入门指南”，让大家快速了解生成型大语言模型是如何工作的。演讲持续了大约 20 分钟（因此，标题是...
