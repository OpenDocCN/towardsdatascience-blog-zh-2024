# Mistral 与 Mixtral：比较 7B、8x7B 和 8x22B 大语言模型

> 原文：[`towardsdatascience.com/mistral-vs-mixtral-comparing-the-7b-8x7b-and-8x22b-large-language-models-58ab5b2cc8ee?source=collection_archive---------2-----------------------#2024-04-20`](https://towardsdatascience.com/mistral-vs-mixtral-comparing-the-7b-8x7b-and-8x22b-large-language-models-58ab5b2cc8ee?source=collection_archive---------2-----------------------#2024-04-20)

## 在 Google Colab 中运行 7B 和 22B 模型

[](https://dmitryelj.medium.com/?source=post_page---byline--58ab5b2cc8ee--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--58ab5b2cc8ee--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--58ab5b2cc8ee--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--58ab5b2cc8ee--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--58ab5b2cc8ee--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--58ab5b2cc8ee--------------------------------) ·阅读时间 10 分钟·2024 年 4 月 20 日

--

![](img/f41d7b4081f76c11b35c9131f4fe12a5.png)

图片由 [Img IX](https://unsplash.com/@imgix) 提供，Unsplash

不久前，所有的 IT 新闻频道都报道了新发布的开源 Mixtral 8x22B 模型，它在 MMLU（大规模多任务语言理解）或 WinoGrande（常识推理）等基准测试中超越了 ChatGPT 3.5。这是开源模型领域的一大成就。学术基准测试自然很有趣，但这个模型在实际操作中表现如何？它的系统要求是什么？与之前的语言模型相比，它真的更好吗？在本文中，我将测试四种不同的模型（7B、8x7B、22B 和 8x22B，带有和不带有“专家混合”架构），我们将一起看到结果。

让我们开始吧！

顺便提一下，我与 Mistral AI 没有任何商业关系，所有的测试都是我个人独立完成的。

## 稀疏专家混合（SMoE）

在大语言模型（LLM）时代刚开始时，人们就已经知道，通常来说，模型越大，智能越强，知识越丰富，结果也越好。但更大的模型也意味着更高的计算成本。如果一个聊天机器人需要 5 分钟才能回应，没人愿意等下去。“专家混合”（mixture of experts）的直观想法是……
