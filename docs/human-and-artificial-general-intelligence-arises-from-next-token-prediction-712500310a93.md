# 人类与人工通用智能源于下一个词的预测

> 原文：[`towardsdatascience.com/human-and-artificial-general-intelligence-arises-from-next-token-prediction-712500310a93?source=collection_archive---------1-----------------------#2024-04-28`](https://towardsdatascience.com/human-and-artificial-general-intelligence-arises-from-next-token-prediction-712500310a93?source=collection_archive---------1-----------------------#2024-04-28)

[](https://rachel-draelos.medium.com/?source=post_page---byline--712500310a93--------------------------------)![Rachel Draelos, MD, PhD](https://rachel-draelos.medium.com/?source=post_page---byline--712500310a93--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--712500310a93--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--712500310a93--------------------------------) [Rachel Draelos, MD, PhD](https://rachel-draelos.medium.com/?source=post_page---byline--712500310a93--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--712500310a93--------------------------------) ·16 分钟阅读·2024 年 4 月 28 日

--

![](img/85569874c8d96b9d8ec78e431992c703.png)

封面图由作者使用 DALLE2 生成

如果人类的智能来自于成功的下一个词预测，那如果下一个词预测是人工通用智能涌现的足够目标函数，会怎样？

这篇文章提出并探讨了这样一个假设：当一个学习系统非常擅长下一个词的预测时，通用智能就会出现。这个假设在工业和学术界的 AI 研究中往往是隐含的，模糊的，或者在边缘游走——但到目前为止，我认为它没有得到足够的公开讨论。这里我从不同的角度探讨这一观点，包括通过讨论现有的 LLM 预训练目标、人类作为预测机器、下一个词预测的有益特性以及缺失的部分。写这篇文章的动机是激发对下一个词预测与智能思维发展之间关系的更深层次兴趣。

# **背景**

上周我开车去公园，突然觉得，如果大脑中的语言中心仅仅是一个下一个词的预测器，那将是多么令人沮丧。大型语言模型通过预测下一个词获得了[令人难以置信的涌现能力](https://glassboxmedicine.com/2023/11/26/sparks-of-artificial-general-intelligence-highlights-from-95-pages/)，那么我的语言智能是否也可能来源于像预测下一个词这样简单的机制？
