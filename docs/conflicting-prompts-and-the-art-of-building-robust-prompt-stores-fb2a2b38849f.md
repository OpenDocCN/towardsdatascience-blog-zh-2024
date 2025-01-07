# 相互冲突的提示与构建企业级提示库的艺术

> 原文：[`towardsdatascience.com/conflicting-prompts-and-the-art-of-building-robust-prompt-stores-fb2a2b38849f?source=collection_archive---------9-----------------------#2024-08-20`](https://towardsdatascience.com/conflicting-prompts-and-the-art-of-building-robust-prompt-stores-fb2a2b38849f?source=collection_archive---------9-----------------------#2024-08-20)

## 基于强化学习的提示库自动化策划

[](https://debmalyabiswas.medium.com/?source=post_page---byline--fb2a2b38849f--------------------------------)![Debmalya Biswas](https://debmalyabiswas.medium.com/?source=post_page---byline--fb2a2b38849f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fb2a2b38849f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb2a2b38849f--------------------------------) [Debmalya Biswas](https://debmalyabiswas.medium.com/?source=post_page---byline--fb2a2b38849f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb2a2b38849f--------------------------------) ·10 分钟阅读·2024 年 8 月 20 日

--

# 介绍

目前，提示是与大型语言模型（LLM）进行互动的主要方式。提示需要根据用户需求进行调整，提供正确的上下文和指导 —— 以最大化获得“正确”响应的机会。

这导致了提示工程学的兴起[1]，作为一门专业学科，提示工程师系统地进行试验，记录他们的发现，以得出能够引发“最佳”响应的“正确”提示。成功的提示列表随后会组织成一个库，便于高效重用 —— 这被称为**提示库**。

不幸的是，策划和维护一个高质量的提示库仍然是**具有挑战性的**。提示库的总体目标是能够为给定的任务检索到最佳提示，而无需重复整个实验过程。然而，这一检索过程并不容易，主要是由于提示的**重叠**特性。

## 问题陈述

让我们尝试借助一些内容创作领域的提示（这是当前生成型人工智能应用最广泛的领域之一）来理解重叠提示的问题：
