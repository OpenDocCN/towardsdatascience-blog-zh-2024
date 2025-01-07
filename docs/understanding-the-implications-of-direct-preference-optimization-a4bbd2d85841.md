# 理解直接偏好优化

> 原文：[`towardsdatascience.com/understanding-the-implications-of-direct-preference-optimization-a4bbd2d85841?source=collection_archive---------0-----------------------#2024-02-18`](https://towardsdatascience.com/understanding-the-implications-of-direct-preference-optimization-a4bbd2d85841?source=collection_archive---------0-----------------------#2024-02-18)

## 回顾《直接偏好优化：你的语言模型秘密地是一个奖励模型》论文及其研究结果

[](https://medium.com/@mgunton7?source=post_page---byline--a4bbd2d85841--------------------------------)![Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--a4bbd2d85841--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a4bbd2d85841--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a4bbd2d85841--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--a4bbd2d85841--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a4bbd2d85841--------------------------------) ·阅读时间 8 分钟·2024 年 2 月 18 日

--

![](img/0b763aeff4b52b196cfe8e0e6785650d.png)

作者通过 DALL-E 提供的图片

*这篇博客文章的灵感来源于我最近与一些朋友关于《直接偏好优化（DPO）》论文的讨论。讨论非常热烈，涵盖了许多关于大语言模型（LLM）和机器学习的核心话题。以下是对其中一些想法和论文中讨论的概念的扩展。*

直接偏好优化（DPO）已经成为新型基础模型微调的主要方法。著名的 Mixtral 8x7B 是由 Mistral 创建的稀疏专家混合模型，通过使用 DPO，能够在显著减少参数的情况下，达到 LLaMa 70B 模型的性能水平。自然，这一成功使得许多社区成员开始用 DPO 对自己的模型进行微调。

让我们深入了解一下什么是 DPO 以及我们是如何走到今天这一步的。

# 高级讨论

首先，我们需要明确从高层次来看微调应该做什么。当你预训练了一个具有强大生成能力的模型后，你通常会希望以某种方式控制它的输出。无论是优化模型使其能够作为聊天机器人进行对话，还是使其能够生成代码……
