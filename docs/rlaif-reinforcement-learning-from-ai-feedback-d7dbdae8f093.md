# RLAIF：来自 AI 反馈的强化学习

> 原文：[`towardsdatascience.com/rlaif-reinforcement-learning-from-ai-feedback-d7dbdae8f093?source=collection_archive---------8-----------------------#2024-01-23`](https://towardsdatascience.com/rlaif-reinforcement-learning-from-ai-feedback-d7dbdae8f093?source=collection_archive---------8-----------------------#2024-01-23)

## 通过自动化人类反馈使 RLHF 的对齐过程更具可扩展性…

[](https://wolfecameron.medium.com/?source=post_page---byline--d7dbdae8f093--------------------------------)![Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--d7dbdae8f093--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d7dbdae8f093--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7dbdae8f093--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--d7dbdae8f093--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7dbdae8f093--------------------------------) ·18 分钟阅读 ·2024 年 1 月 23 日

--

![](img/e6700ce9041c1d6c1e5ca6dbf2d86e49.png)

（图片来源：[Rock’n Roll Monkey](https://unsplash.com/@rocknrollmonkey?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/gray-and-white-robot-illustration-FTfjMijq-Ws?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

除了使用更大模型和数据集进行预训练外，大型语言模型（LLM）质量的显著提升还得益于对齐过程的进展，这主要是通过细化技术如监督式微调（SFT）和来自人类反馈的强化学习（RLHF）推动的。特别是 RLHF 是一种有趣的技术，它使我们能够基于人类提供的偏好直接微调语言模型。简而言之，我们可以直接教导模型生成符合人类偏好的输出，这是一个灵活且强大的框架。然而，这要求收集大量人类偏好标签，而这通常既昂贵又耗时。在本综述中，我们将探讨旨在利用 AI 自动化收集 RLHF 所需人类偏好的最新研究，形成一种新的技术，称为来自 AI 反馈的强化学习（RLAIF）。

# 训练语言模型

语言模型的训练过程分为几个阶段；见上文。首先，我们在大量未标注的文本数据上进行预训练，这是训练中最昂贵的部分。预训练后，我们执行一个三阶段的对齐过程…
