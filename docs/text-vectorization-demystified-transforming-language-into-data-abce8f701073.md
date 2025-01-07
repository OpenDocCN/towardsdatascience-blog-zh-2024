# 文本向量化解密：将语言转化为数据

> 原文：[`towardsdatascience.com/text-vectorization-demystified-transforming-language-into-data-abce8f701073?source=collection_archive---------3-----------------------#2024-08-03`](https://towardsdatascience.com/text-vectorization-demystified-transforming-language-into-data-abce8f701073?source=collection_archive---------3-----------------------#2024-08-03)

## 一份关于文本向量化的直观指南

[](https://medium.com/@lakshmi.sunil5486?source=post_page---byline--abce8f701073--------------------------------)![Lakshmi Narayanan](https://medium.com/@lakshmi.sunil5486?source=post_page---byline--abce8f701073--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--abce8f701073--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abce8f701073--------------------------------) [Lakshmi Narayanan](https://medium.com/@lakshmi.sunil5486?source=post_page---byline--abce8f701073--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abce8f701073--------------------------------) ·12 分钟阅读·2024 年 8 月 3 日

--

在我上一篇[文章](https://medium.com/@lakshmi.sunil5486/diving-into-foundation-models-and-large-language-models-a-beginners-guide-0560f0996219)中，我们深入探讨了基础模型和大型语言模型（LLMs）。我们尝试了解它们是什么，它们是如何使用的，以及它们的特别之处。我们探索了它们在哪些方面表现优异，哪些方面可能存在不足。我们讨论了它们在理解文本和生成内容等不同领域的应用。这些 LLMs 在自然语言处理（NLP）领域带来了变革。

当我们谈论 NLP 管道时，特征工程（也称为*特征提取*、*文本表示*或*文本向量化*）是一个非常重要且不可或缺的步骤。这个步骤涉及使用技术将文本表示为数字（特征向量）。在处理 NLP 问题时我们需要进行这一步骤，因为计算机无法理解文本，它们只能理解数字，正是这种文本的数值表示需要输入到机器学习算法中，用于解决各种基于文本的应用案例，如语言翻译、情感分析、摘要生成等。

对于我们这些了解机器学习管道的人来说，我们明白特征工程是从模型中获得良好结果的关键步骤。这一概念在 NLP 中同样适用。当我们生成文本数据的数值表示时，我们努力实现的一个重要目标是，这个数值表示生成的***应该***…
