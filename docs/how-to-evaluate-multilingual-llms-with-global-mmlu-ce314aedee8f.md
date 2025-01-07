# 如何评估任何语言中的多语言 LLM

> 原文：[`towardsdatascience.com/how-to-evaluate-multilingual-llms-with-global-mmlu-ce314aedee8f?source=collection_archive---------7-----------------------#2024-12-09`](https://towardsdatascience.com/how-to-evaluate-multilingual-llms-with-global-mmlu-ce314aedee8f?source=collection_archive---------7-----------------------#2024-12-09)

## 在全球大规模多任务语言理解（Global-MMLU）基准上，评估特定语言 LLM 的准确性（使用 Python）

[](https://medium.com/@leoneversberg?source=post_page---byline--ce314aedee8f--------------------------------)![Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--ce314aedee8f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ce314aedee8f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ce314aedee8f--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--ce314aedee8f--------------------------------)

·发表于[《Towards Data Science》](https://towardsdatascience.com/?source=post_page---byline--ce314aedee8f--------------------------------) ·8 分钟阅读·2024 年 12 月 9 日

--

![](img/f7fc65514649f1df9e4413d80ee13bb0.png)

图片来自[Benjamin Kaufmann](https://unsplash.com/@devnull?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

每当一个新的大型语言模型（LLM）发布时，我们自然会问自己一个明显的问题：这个 LLM 是否比我当前使用的更好？

LLM 通常会在大量基准测试中进行评估，其中大多数基准仅为英语。

对于多语言模型来说，能够找到每种特定语言的评估指标是非常罕见的，这些语言出现在训练数据中。

有时评估指标是针对基础模型发布的，而不是针对经过指令微调的模型。通常，评估也不会在我们实际本地使用的量化模型上进行。

因此，很难找到在英语以外的特定语言中，来自多个大型语言模型（LLM）的可比较评估结果。

因此，在本文中，我们将使用全球大规模多任务语言理解（Global-MMLU）数据集，并通过我们选择的语言，利用广泛使用的 MMLU 基准进行评估。

## 目录
