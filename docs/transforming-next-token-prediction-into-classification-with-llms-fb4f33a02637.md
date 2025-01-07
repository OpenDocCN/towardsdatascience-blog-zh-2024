# 将下一个标记预测转化为 LLM 分类任务

> 原文：[`towardsdatascience.com/transforming-next-token-prediction-into-classification-with-llms-fb4f33a02637?source=collection_archive---------3-----------------------#2024-06-20`](https://towardsdatascience.com/transforming-next-token-prediction-into-classification-with-llms-fb4f33a02637?source=collection_archive---------3-----------------------#2024-06-20)

## 从标记到标签：使用大型语言模型进行分类

[](https://medium.com/@yuchengtsai84?source=post_page---byline--fb4f33a02637--------------------------------)![Yu-Cheng Tsai](https://medium.com/@yuchengtsai84?source=post_page---byline--fb4f33a02637--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fb4f33a02637--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb4f33a02637--------------------------------) [Yu-Cheng Tsai](https://medium.com/@yuchengtsai84?source=post_page---byline--fb4f33a02637--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb4f33a02637--------------------------------) ·阅读时间 6 分钟·2024 年 6 月 20 日

--

![](img/b7df3c282346bf90d1b8ab34a4d1fd47.png)

图片来源：[Myles Bloomfield](https://unsplash.com/@loomydoons?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

免费[链接](https://medium.com/towards-data-science/transforming-next-token-prediction-into-classification-with-llms-fb4f33a02637?sk=c5693c58c60d47d6a1846cbafffd1b8a)! 感谢，祝您阅读愉快。:)

大型语言模型（LLMs）通过在大量互联网数据上进行训练，展现出多功能性，可以执行各种自然语言任务。其中一个常见应用是分类，这是一个将主题分为预定义标签的监督学习任务。[零-shot](https://huggingface.co/tasks/zero-shot-classification)和[少-shot 分类](https://huggingface.co/blog/few-shot-learning-gpt-neo-and-inference-api)已经成为流行技术，使得 LLMs 能够在没有训练数据或仅有少量示例的情况下执行分类任务。然而，研究表明，为了提高准确性，指令微调能够通过使用精心挑选的数据集对 LLMs 进行调优，从而提高其性能。

# 指令微调大型语言模型（LLMs）

常见的指令微调方法是构建一个包含问答对的数据集。然后，使用这些数据对预训练的 LLM 进行监督式的进一步微调。

你可以查看我之前的文章了解这种方法。
