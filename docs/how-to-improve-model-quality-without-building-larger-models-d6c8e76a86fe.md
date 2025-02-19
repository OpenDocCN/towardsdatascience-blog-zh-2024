# 如何在不构建更大模型的情况下提高模型质量

> 原文：[`towardsdatascience.com/how-to-improve-model-quality-without-building-larger-models-d6c8e76a86fe?source=collection_archive---------5-----------------------#2024-10-08`](https://towardsdatascience.com/how-to-improve-model-quality-without-building-larger-models-d6c8e76a86fe?source=collection_archive---------5-----------------------#2024-10-08)

## 进入谷歌 DeepMind 的“**Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters**”

[](https://medium.com/@mgunton7?source=post_page---byline--d6c8e76a86fe--------------------------------)![Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--d6c8e76a86fe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6c8e76a86fe--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6c8e76a86fe--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--d6c8e76a86fe--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6c8e76a86fe--------------------------------) ·阅读时间 11 分钟·2024 年 10 月 8 日

--

![](img/e481b2de6c28675a6758b1d595d75bb1.png)

作者提供的图片 — Flux.1 12B

最近，OpenAI 推出了他们最新的模型 o1。与其突出该模型的参数规模，OpenAI 更强调的是该模型因为花费更多时间而表现得更好。当你向模型提问时，它通常需要几秒钟来响应——这与大多数人现在对大型语言模型（LLM）期望的毫秒级响应速度相去甚远。尽管如此，这额外的时间似乎是值得的，因为 o1 在 LMSYS Chatbot Arena 的得分远高于其他模型。

鉴于这一性能飞跃，每个人都在问：他们是怎么做到的？

![](img/acc74e222738f0b3c3d8b5d28e79cf8b.png)

[Lmsys Chatbot Arena](https://lmarena.ai/) 2024 年 9 月 23 日的数学排名屏幕截图

尽管 OpenAI 尚未公开声明他们是如何取得这些成果的，但最近有几篇论文可能为我们揭示了幕后发生的事情。其中一篇论文是[“**Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters**”](https://arxiv.org/pdf/2408.03314)。这篇论文探讨了如何利用……
