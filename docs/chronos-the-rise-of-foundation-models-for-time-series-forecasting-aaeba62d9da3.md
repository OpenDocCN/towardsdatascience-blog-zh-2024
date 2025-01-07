# Chronos：基础模型在时间序列预测中的崛起

> 原文：[https://towardsdatascience.com/chronos-the-rise-of-foundation-models-for-time-series-forecasting-aaeba62d9da3?source=collection_archive---------0-----------------------#2024-04-05](https://towardsdatascience.com/chronos-the-rise-of-foundation-models-for-time-series-forecasting-aaeba62d9da3?source=collection_archive---------0-----------------------#2024-04-05)

## 探索Chronos：基础AI模型如何在预测分析中设立新标准

[](https://medium.com/@luisroque?source=post_page---byline--aaeba62d9da3--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--aaeba62d9da3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aaeba62d9da3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aaeba62d9da3--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--aaeba62d9da3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aaeba62d9da3--------------------------------) ·阅读时间16分钟·2024年4月5日

--

*本文由Rafael Guedes共同撰写。*

# 介绍

时间序列预测由于在其他人工智能（AI）领域的成功，正在向基础模型发展。特别是，我们已经见证了这种方法在自然语言处理（NLP）中的成功。基础模型的发展步伐随着时间的推移而加速。每个月都会发布一个新的、更强大的大语言模型（LLM）。这不仅仅局限于NLP。我们在计算机视觉领域也看到了类似的增长模式。像Meta的Segment Anything Model（SAM）[1]这样的分割模型可以识别并精确分割未知图像中的物体。像LLaVa[2]或Qwen-VL[3]这样的多模态模型能够处理文本和图像，以回答任何用户问题。这些模型的共同特征是它们能够执行准确的零样本推理，这意味着它们无需在你的数据上进行训练便能表现出色。

在这个阶段，定义什么是基础模型，以及它与传统方法有何不同，可能会有助于理解。首先，基础模型是大规模的（即其训练过程），它提供了对我们在数据中可以发现的主要模式和重要细微差别的广泛理解。
