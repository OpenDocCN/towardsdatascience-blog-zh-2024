# 我测试了前沿的多模态 LLM 在图表解读技能上的表现

> 原文：[`towardsdatascience.com/mulitmodal-llms-interpreting-charts-b212f5c0aa1f?source=collection_archive---------10-----------------------#2024-11-05`](https://towardsdatascience.com/mulitmodal-llms-interpreting-charts-b212f5c0aa1f?source=collection_archive---------10-----------------------#2024-11-05)

## 多模态 LLM 能准确推断基础图表吗？

[](https://thuwarakesh.medium.com/?source=post_page---byline--b212f5c0aa1f--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--b212f5c0aa1f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b212f5c0aa1f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b212f5c0aa1f--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--b212f5c0aa1f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b212f5c0aa1f--------------------------------) ·31 分钟阅读·2024 年 11 月 5 日

--

![](img/846e609dff1734aba2cf8014b5f12005.png)

图片由作者使用 Flux 1.1 [Pro]制作

多模态 LLM（MLLM）承诺它们能够解读图像上的任何内容。对于大多数情况来说，这是真的，例如图像描述和物体检测。

但是它能合理且准确地理解图表上的数据吗？

如果你真的想开发一个应用程序，让它在你把相机对准汽车仪表盘时告诉你该做什么，那么 LLM 在图表解读技能上的表现应该是出色的。

当然，多模态 LLM 能够叙述图表上的内容，但理解数据并回答复杂的用户问题仍然具有挑战性。

我想知道这有多难。

我为 LLM 设置了八个挑战任务。每个任务都有一个基础图表以及一个问题，LLM 需要基于图表给出答案。我们知道正确答案，因为是我们创建了这些数据，但 LLM 只需利用给定的可视化信息推理出答案。

截至本文写作时，根据我的理解，市场上有五家主要的多模态大语言模型（LLM）提供商：OpenAI（GPT4o），Meta 的 Llama 3.2（11B 和 90B 模型），Mistral 及其全新的 Pixtral 12B，Cloude 3.5 Sonnet，以及 Google 的 Gemini 1.5。
