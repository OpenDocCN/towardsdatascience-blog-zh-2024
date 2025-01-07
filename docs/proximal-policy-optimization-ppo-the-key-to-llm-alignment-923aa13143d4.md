# **近端策略优化（PPO）**：大语言模型对齐的关键

> 原文：[`towardsdatascience.com/proximal-policy-optimization-ppo-the-key-to-llm-alignment-923aa13143d4?source=collection_archive---------7-----------------------#2024-02-15`](https://towardsdatascience.com/proximal-policy-optimization-ppo-the-key-to-llm-alignment-923aa13143d4?source=collection_archive---------7-----------------------#2024-02-15)

## 现代策略梯度算法及其在语言模型中的应用……

[](https://wolfecameron.medium.com/?source=post_page---byline--923aa13143d4--------------------------------)![Cameron R. Wolfe, 博士](https://wolfecameron.medium.com/?source=post_page---byline--923aa13143d4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--923aa13143d4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--923aa13143d4--------------------------------) [Cameron R. Wolfe, 博士](https://wolfecameron.medium.com/?source=post_page---byline--923aa13143d4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--923aa13143d4--------------------------------) ·18 分钟阅读·2024 年 2 月 15 日

--

![](img/bd5ca84ae9538a8e1d8a9539273ae459.png)

（照片由[Daniel Olah](https://unsplash.com/@danesduet?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/white-lines-on-track-field-2cHW5TKr9Vs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

最近的 AI 研究表明，强化学习（RL）——特别是*人类反馈强化学习（RLHF）*——是训练大语言模型（LLM）的关键组成部分。然而，许多 AI 从业者（诚然）避免使用 RL，原因有很多，包括对 RL 的不熟悉或更偏好监督学习技术。确实存在反对使用 RL 的合理论点；例如，收集人类偏好数据成本高昂，并且 RL 可能数据效率低下。然而，*我们不应仅仅因为缺乏理解或不熟悉而避免使用 RL*！这些技术并不难以理解，正如多篇近期论文所展示的，它们可以极大地提升 LLM 的表现。

本概述是系列文章的第三部分，旨在揭开 RL 的神秘面纱，并展示其如何用于训练 LLM。虽然到目前为止我们主要介绍了与 RL 相关的基础概念，但接下来我们将深入探讨为语言模型对齐奠定基础的算法——*近端策略优化（PPO）* [2]。正如我们将看到的，PPO 表现良好，且极其容易理解和使用，使其成为一个从实际角度来看非常理想的算法。正因如此，PPO…
