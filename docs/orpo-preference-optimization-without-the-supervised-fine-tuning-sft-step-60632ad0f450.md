# ORPO：无监督微调（SFT）步骤的偏好优化

> 原文：[https://towardsdatascience.com/orpo-preference-optimization-without-the-supervised-fine-tuning-sft-step-60632ad0f450?source=collection_archive---------4-----------------------#2024-04-10](https://towardsdatascience.com/orpo-preference-optimization-without-the-supervised-fine-tuning-sft-step-60632ad0f450?source=collection_archive---------4-----------------------#2024-04-10)

## 一种比 DPO 更便宜的对齐方法，性能相当

[](https://medium.com/@bnjmn_marie?source=post_page---byline--60632ad0f450--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--60632ad0f450--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60632ad0f450--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--60632ad0f450--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--60632ad0f450--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60632ad0f450--------------------------------) ·阅读时长 7 分钟·2024 年 4 月 10 日

--

![](../Images/4d246d66e25e62577f87b7c194f20d7b.png)

由 DALL-E 生成

现在有许多方法可以将大型语言模型（LLM）与人类偏好对齐。带有人工反馈的强化学习（RLHF）是最早的一种方法，并且催生了 ChatGPT，但 RLHF 成本非常高。DPO、IPO 和 KTO 比 RLHF 显著便宜，因为它们不需要奖励模型。

虽然 DPO 和 IPO 更便宜，但它们仍然需要训练两个不同的模型。一个模型用于监督微调（SFT）步骤，即训练模型回答指令，然后使用该 SFT 模型进行初始化和作为参考，来对齐人类偏好。

ORPO 是另一种新的大型语言模型（LLM）对齐方法，但这一方法甚至不需要 SFT 模型。使用 ORPO，LLM 可以共同学习如何回答指令和人类偏好。

在这篇文章中，我将解释 ORPO 并回顾其性能。我展示了如何使用它将 Mistral 7B 转换为一个聊天模型，使用普通消费者硬件即可实现。

# 联合 SFT 和偏好优化

本文介绍了 ORPO：

[ORPO：无参考模型的单体偏好优化](https://arxiv.org/abs/2403.07691)
