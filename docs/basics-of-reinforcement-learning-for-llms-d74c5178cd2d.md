# 强化学习基础：适用于大语言模型（LLMs）

> 原文：[https://towardsdatascience.com/basics-of-reinforcement-learning-for-llms-d74c5178cd2d?source=collection_archive---------5-----------------------#2024-01-31](https://towardsdatascience.com/basics-of-reinforcement-learning-for-llms-d74c5178cd2d?source=collection_archive---------5-----------------------#2024-01-31)

## 理解强化学习（RL）的问题表述和基本算法

[](https://wolfecameron.medium.com/?source=post_page---byline--d74c5178cd2d--------------------------------)[![Cameron R. Wolfe, Ph.D.](../Images/52bb88d7cf1105501be2fae5ccbe7a03.png)](https://wolfecameron.medium.com/?source=post_page---byline--d74c5178cd2d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d74c5178cd2d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d74c5178cd2d--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--d74c5178cd2d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d74c5178cd2d--------------------------------) ·18分钟阅读·2024年1月31日

--

![](../Images/3e89f8565100e5322bcdaf6af0d84e22.png)

（照片由[Ricardo Gomez Angel](https://unsplash.com/@rgaleriacom?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/grayscale-photo-of-metal-mesh-screen-z6CcN8rlftY?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

最近的人工智能研究表明，强化学习 —— *更具体地说，* [*来自人类反馈的强化学习（RLHF）*](https://magazine.sebastianraschka.com/p/llm-training-rlhf-and-its-alternatives) —— 是训练最先进的大型语言模型（LLM）的关键组成部分。尽管如此，大多数关于语言模型的开源研究仍然大力强调监督学习策略，如监督微调（SFT）。这种对强化学习的忽视可以归因于多个因素，包括需要整理人类偏好数据或进行高质量RLHF所需的数据量。然而，无法忽视的一个因素，可能是对强化学习持怀疑态度的根本原因，就是它不像监督学习那样普遍使用。因此，AI从业者（包括我自己！）由于简单的理解不足，往往避免使用强化学习 —— *我们倾向于坚持使用我们最熟悉的方法*。

> “我们中许多人表达了对监督标注的偏好，因为其信号更密集…然而，强化学习证明了其高度有效性，尤其是在成本和时间效率方面。” *— 引自[8]*

**本系列文章。** 在接下来的几个概述中，我们将通过构建…来解决这个问题。
