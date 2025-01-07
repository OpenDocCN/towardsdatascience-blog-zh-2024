# Moirai：面向通用预测的时间序列基础模型

> 原文：[`towardsdatascience.com/moirai-time-series-foundation-models-for-universal-forecasting-dc93f74b330f?source=collection_archive---------0-----------------------#2024-04-11`](https://towardsdatascience.com/moirai-time-series-foundation-models-for-universal-forecasting-dc93f74b330f?source=collection_archive---------0-----------------------#2024-04-11)

## 预测分析的未来：探索 Moirai，Salesforce 推出的用于先进时间序列预测的新基础模型

[](https://medium.com/@luisroque?source=post_page---byline--dc93f74b330f--------------------------------)![Luís Roque](https://medium.com/@luisroque?source=post_page---byline--dc93f74b330f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc93f74b330f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc93f74b330f--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--dc93f74b330f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc93f74b330f--------------------------------) ·15 分钟阅读·2024 年 4 月 11 日

--

*这篇文章由 Rafael Guedes 和我共同撰写。*

# 引言

时间序列基础模型的开发在过去两个季度加速推进，我们几乎见证了每个月都有新模型的发布。从 2023 年最后一个季度的 TimeGPT [1]开始，到 Lag-Llama [2]的发布，再到 Google 推出 TimesFM [3]、Amazon 推出 Chronos [4]，以及 Salesforce 发布 Moirai [5]，这一过程正在加速。

为了理解对基础模型日益增长的兴趣，我们应该定义它们的核心能力：零-shot 推理。零-shot 推理指的是这些模型能够在训练过程中从未接触过的数据上准确执行任务或做出预测的能力。这种能力已被探索应用于各个领域的模型，例如自然语言处理（NLP）、计算机视觉以及多模态任务（结合文本、图像等）。术语“零-shot”来自于这样一个概念：模型在训练期间未曾见过来自某个特定任务或数据领域的任何示例，但它能够有效地“射击”或瞄准该领域的任务。这个术语最早出现在 Hinton 等人发表的论文《Zero-Shot Learning with Semantic Output Codes》中，并在 NIPS 会议上进行了介绍…
