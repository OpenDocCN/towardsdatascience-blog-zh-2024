# 我是如何从零开始构建一个基于大型语言模型（LLM）的游戏

> 原文：[https://towardsdatascience.com/how-i-built-an-llm-based-game-from-scratch-86ac55ec7a10?source=collection_archive---------12-----------------------#2024-06-11](https://towardsdatascience.com/how-i-built-an-llm-based-game-from-scratch-86ac55ec7a10?source=collection_archive---------12-----------------------#2024-06-11)

## 第一部分：游戏概念与LLM的因果图

[](https://medium.com/@jacky.kaub?source=post_page---byline--86ac55ec7a10--------------------------------)[![Jacky Kaub](../Images/e66c699ee5a9d5bbd58a1a72d688234a.png)](https://medium.com/@jacky.kaub?source=post_page---byline--86ac55ec7a10--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--86ac55ec7a10--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--86ac55ec7a10--------------------------------) [Jacky Kaub](https://medium.com/@jacky.kaub?source=post_page---byline--86ac55ec7a10--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86ac55ec7a10--------------------------------) ·14分钟阅读·2024年6月11日

--

![](../Images/d29c76dd2a5b5b6fd39bc52508bd43e2.png)

LLM游戏的主页，由Dalle 3生成

# 引言

几个月前，我和一个朋友就大型语言模型（LLM）在游戏设计中的应用展开了辩论。

我的朋友认为LLM太不可预测，无法在游戏中稳定使用，不应该用于“实时机制”，而我则相信，如果通过正确的框架进行控制，它们可以提供创新的体验。

这场讨论促使我开始了一个新的副项目，并且这个项目已经成为我最为自豪的成就。

本系列文章旨在提供我在过去几个月中所做的所有工作的高级视角，以及截至目前我仍需面对的挑战。

以下是展示截至2024年2月的游戏短视频。

在本文中，我们将重点介绍游戏的核心概念，并详细说明我设想的架构，以应对我遇到的一些最大问题，例如信息泄露和幻觉。
