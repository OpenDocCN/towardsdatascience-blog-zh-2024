# 使用 Transformer 进行时间序列预测

> 原文：[`towardsdatascience.com/time-series-prediction-with-transformers-2b64478a4cbd?source=collection_archive---------4-----------------------#2024-01-16`](https://towardsdatascience.com/time-series-prediction-with-transformers-2b64478a4cbd?source=collection_archive---------4-----------------------#2024-01-16)

## 《Pytorch 中的 Transformer 完整指南》

[](https://medium.com/@hrmnmichaels?source=post_page---byline--2b64478a4cbd--------------------------------)![Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--2b64478a4cbd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2b64478a4cbd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b64478a4cbd--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--2b64478a4cbd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b64478a4cbd--------------------------------) ·17 分钟阅读·2024 年 1 月 16 日

--

自从[Feynman 学习法](https://chat.openai.com/)的出现以来，[大型语言模型](https://en.wikipedia.org/wiki/Large_language_model)（LLMs）已掀起了巨大的热潮，甚至连 AI 领域以外的人也有所耳闻。尽管需要理解的是，[LLMs 本质上是“仅仅”是序列预测模型](https://www.linkedin.com/posts/yann-lecun_i-have-claimed-that-auto-regressive-llms-activity-7045908925660950528-hJGk)，并不具备任何形式的智能或推理——但它们取得的成果无疑是非常令人印象深刻的，甚至有些人谈论这是“AI 革命”的另一步。

LLMs 成功的关键在于其核心构建模块——**Transformer**。在本文中，我们将提供在 Pytorch 中使用 Transformer 的完整指南，特别关注时间序列预测。感谢您的到来，希望您能享受这段旅程！

![](img/b34cf6d50a71d4ef98f9bdb04c6a7be8.png)

图片来源：[Tim Meyer](https://unsplash.com/@timmeyer?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于[Unsplash](https://unsplash.com/photos/yellow-chevrolet-coupe-close-up-photography-GIm7wxiAZys?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

有人可能会认为，所有通过变压器（transformers）解决的问题本质上都是时间序列问题。虽然这确实是事实，但在这里我们将特别关注连续系列和数据——例如预测疾病传播或天气预报。与自然语言处理（[NLP](https://en.wikipedia.org/wiki/Natural_language_processing)）的主要应用不同（简单来说，如果这个词在此语境下是允许的——开发一个像 ChatGPT 这样的模型并使其自然运行确实需要大量进一步的优化步骤和技巧），主要区别在于输入空间是连续的，而 NLP 使用的是离散的词元（tokens）。然而，除了……
