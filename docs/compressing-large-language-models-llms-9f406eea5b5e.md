# 压缩大型语言模型（LLMs）

> 原文：[`towardsdatascience.com/compressing-large-language-models-llms-9f406eea5b5e?source=collection_archive---------4-----------------------#2024-08-30`](https://towardsdatascience.com/compressing-large-language-models-llms-9f406eea5b5e?source=collection_archive---------4-----------------------#2024-08-30)

## **在不牺牲性能的情况下将大型语言模型（LLMs）缩小 10 倍**

[](https://shawhin.medium.com/?source=post_page---byline--9f406eea5b5e--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--9f406eea5b5e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9f406eea5b5e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9f406eea5b5e--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--9f406eea5b5e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9f406eea5b5e--------------------------------) ·10 分钟阅读·2024 年 8 月 30 日

--

![](img/ca5838eea1347b47a647ce68d9693320.png)

图片来自 Canva。

本文是关于在实践中使用大型语言模型（LLMs）[更大系列](https://shawhin.medium.com/list/large-language-models-llms-8e009ae3054c)的一部分。虽然 LLMs 的巨大规模赋予了它们在各种用例中的出色表现，但这也带来了**在实际问题中应用的挑战**。在本文中，我将讨论如何通过压缩 LLMs 来克服这些挑战。我将首先提供一个关键概念的高层次概述，然后通过一个具体的 Python 代码示例进行讲解。

2023 年的 AI 口号是*“越大越好”*，改进语言模型的公式相当简单：**更多数据 + 更多参数 + 更多计算 = 更好的性能**[1]。

虽然这可能仍然是事实（GPT-5 即将推出？），但使用 100B+参数模型确实存在明显的挑战。例如，一个使用 FP16 的 100B 参数模型，仅存储就需要 200GB！

不用说，大多数消费电子设备（例如手机、平板电脑、笔记本电脑）无法处理这么大的模型……
