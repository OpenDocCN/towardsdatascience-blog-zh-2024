# 大型语言模型，GPT-2——语言模型是无监督的多任务学习者

> 原文：[`towardsdatascience.com/large-language-models-gpt-2-language-models-are-unsupervised-multitask-learners-33440081f808?source=collection_archive---------2-----------------------#2024-02-10`](https://towardsdatascience.com/large-language-models-gpt-2-language-models-are-unsupervised-multitask-learners-33440081f808?source=collection_archive---------2-----------------------#2024-02-10)

## 通过将 GPT 转化为一个强大的多任务零样本模型，充分发挥其能力

[](https://medium.com/@slavahead?source=post_page---byline--33440081f808--------------------------------)![Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--33440081f808--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--33440081f808--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33440081f808--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--33440081f808--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33440081f808--------------------------------) ·阅读时长 6 分钟·2024 年 2 月 10 日

--

![](img/a63584ba23ef556365a591a63499b5af.png)

# 引言

**GPT**是一个广为人知的模型系列，当前的最新版本在各种自然语言处理任务中占据主导地位。第一个 GPT 版本是一个重要的里程碑：该模型在 120M 参数上进行训练，并在多个顶级基准测试中展示了最先进的性能。从这一点开始，研究人员尝试改进基础版本。

2019 年，OpenAI 的研究人员正式发布了 GPT-2。它比 GPT-1 大了 10 倍，这使得其性能得到了进一步提升。除此之外，作者在其工作中推测，**LLMs 是多任务学习者**，意味着它们能够同时学习执行多个任务。这个重要的声明使得在一个更加高效的框架内进一步发展 LLMs 成为可能。

在本文中，我们将通过回顾官方的[GPT-2 论文](https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)，深入探讨其主要方面和相对于 GPT-1 的改进，并了解构建大语言模型（LLMs）的一种新方法。

> 注：本文假设你已经熟悉 GPT 的第一个版本。如果不熟悉，可以查看[这篇文章](https://medium.com/towards-data-science/large-language-models-gpt-1-generative-pre-trained-transformer-7b895f296d3b)。
