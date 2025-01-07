# 扩展单义性：Anthropic 迈向可解释和可操控 LLMs 的一步

> 原文：[`towardsdatascience.com/scaling-monosemanticity-anthropics-one-step-towards-interpretable-manipulable-llms-4b9403c4341e?source=collection_archive---------7-----------------------#2024-05-28`](https://towardsdatascience.com/scaling-monosemanticity-anthropics-one-step-towards-interpretable-manipulable-llms-4b9403c4341e?source=collection_archive---------7-----------------------#2024-05-28)

## GenAI 字节：每天一字节，驱走冒名顶替综合症

## 从提示工程到激活工程，为了实现更可控和更安全的 LLMs

[](https://medium.com/@jacklingenai?source=post_page---byline--4b9403c4341e--------------------------------)![Jack Chih-Hsu Lin](https://medium.com/@jacklingenai?source=post_page---byline--4b9403c4341e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4b9403c4341e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4b9403c4341e--------------------------------) [Jack Chih-Hsu Lin](https://medium.com/@jacklingenai?source=post_page---byline--4b9403c4341e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4b9403c4341e--------------------------------) ·阅读时间 11 分钟·2024 年 5 月 28 日

--

# 背景/介绍

## 单义性与多义性

在神经网络中（LLMs 的算法类型，灵感来源于人类大脑），神经元通常是*多义性的*，这意味着它们的激活响应多个含义和概念。另一方面，如果每个神经元仅代表一个含义/概念，则它们是*单义性的*。例如，短语“I feel blue”和“I’m heavy-hearted”作为 LLMs 的输入，只激活同一组代表悲伤或负面情绪的神经元。这些悲伤神经元也不会被其他概念激活。

## 特性

在机器学习中，“特性”指的是作为输入使用的可测量/可观察的属性。

然而需要注意的是，在本研究中，“特性”指的有所不同。“特性”是指在稀疏自编码器的编码层输出的高维层，它被训练用来预测 LLM 中间的激活。（请参见下面的“方法”部分以了解详细信息）。从高层次看，我将“特性”解释为……
