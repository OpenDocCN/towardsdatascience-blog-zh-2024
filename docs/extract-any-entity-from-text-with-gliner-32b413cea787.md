# 使用GLiNER从文本中提取任何实体

> 原文：[https://towardsdatascience.com/extract-any-entity-from-text-with-gliner-32b413cea787?source=collection_archive---------2-----------------------#2024-03-24](https://towardsdatascience.com/extract-any-entity-from-text-with-gliner-32b413cea787?source=collection_archive---------2-----------------------#2024-03-24)

## *GLiNER是一个命名实体识别（NER）模型，能够通过双向变换器编码器（类似于BERT）识别任何类型的实体，并在零-shot令牌分类任务中超越ChatGPT和其他大型语言模型（LLM）。*

[](https://medium.com/@theDrewDag?source=post_page---byline--32b413cea787--------------------------------)[![Andrea D'Agostino](../Images/58c7c218815f25278aae59cea44d8771.png)](https://medium.com/@theDrewDag?source=post_page---byline--32b413cea787--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--32b413cea787--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--32b413cea787--------------------------------) [Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--32b413cea787--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--32b413cea787--------------------------------) ·6分钟阅读·2024年3月24日

--

![](../Images/06935d5718397d955c5034b3db42fd79.png)

图片来源：[Matt Hardy](https://unsplash.com/@matthardy?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

曾经使用过NER（命名实体识别）范式的人都深知，拥有一个在特定任务上表现出色的模型是多么重要。

事实上，**NER模型对于数据挖掘和文本分析任务极为有用** —— 它们是所有数字智能任务的基础，并且与更大、更复杂的数据科学管道中的各种任务息息相关。

从事NER的人也深知，由于训练阶段需要指定大量标签，训练这样一个模型是多么复杂。像SpaCy和基于变换器的Hugging Face模型等库，极大地帮助了数据科学家以更加高效的方式开发NER模型，并且在一定程度上持续改善这一过程。

在本文中，我们将一起探讨**GLiNER范式，一种结合经典NER范式与大型语言模型（LLM）强大能力的新型实体提取技术。**
