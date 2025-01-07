# QLoRA — 如何在单个 GPU 上微调大型语言模型（LLM）

> 原文：[`towardsdatascience.com/qlora-how-to-fine-tune-an-llm-on-a-single-gpu-4e44d6b5be32?source=collection_archive---------1-----------------------#2024-02-22`](https://towardsdatascience.com/qlora-how-to-fine-tune-an-llm-on-a-single-gpu-4e44d6b5be32?source=collection_archive---------1-----------------------#2024-02-22)

## 包含 Python 示例代码的介绍（以 Mistral-7b 为例）

[](https://shawhin.medium.com/?source=post_page---byline--4e44d6b5be32--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--4e44d6b5be32--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4e44d6b5be32--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4e44d6b5be32--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--4e44d6b5be32--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4e44d6b5be32--------------------------------) ·16 分钟阅读·2024 年 2 月 22 日

--

这篇文章是关于在实践中使用大型语言模型（LLM）系列文章的一部分。[更大的系列](https://shawhin.medium.com/list/large-language-models-llms-8e009ae3054c)中，我们在上一篇文章中展示了如何使用 OpenAI 微调 LLM。然而，这种方法的主要限制在于，OpenAI 的模型通过其 API 隐藏，这限制了我们能够用它们构建的内容和方式。在这里，我将讨论一种使用开源模型和 QLoRA 微调 LLM 的替代方法。

![](img/68d9cb7567d5ae053a881cfeea25ba43.png)

图片来自[Dell](https://unsplash.com/@dell?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**微调**是指我们**对现有模型进行调整，以适应特定的使用场景**。这是最近 AI 创新爆炸性增长中的一个关键部分，催生了像 ChatGPT 这样的产品。

虽然微调是一个简单（且强大）的概念，但将其应用于 LLM 并不总是那么直接。关键挑战在于**LLM 是（非常）计算密集型的**（也就是说，它们不是那种可以在典型的笔记本电脑上训练的模型）。

例如，微调一个 70B 参数的模型需要超过 1TB 的内存[1]。作为参考，一张 A100 GPU 最多可提供 80GB 的内存，因此你（在最好的情况下）需要超过十几张这些价值 2 万美元的显卡！

虽然这可能会让你对构建自定义 AI 的梦想感到沮丧，但还不要放弃。开源社区一直在努力…
