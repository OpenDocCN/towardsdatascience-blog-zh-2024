# 语言模型的训练与推理：从概念到代码

> 原文：[`towardsdatascience.com/language-model-training-and-inference-from-concept-to-code-483cf9b305ef?source=collection_archive---------6-----------------------#2024-01-06`](https://towardsdatascience.com/language-model-training-and-inference-from-concept-to-code-483cf9b305ef?source=collection_archive---------6-----------------------#2024-01-06)

## 学习和实现下一个词预测与一种简单的语言模型...

[](https://wolfecameron.medium.com/?source=post_page---byline--483cf9b305ef--------------------------------)![Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--483cf9b305ef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--483cf9b305ef--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--483cf9b305ef--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--483cf9b305ef--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--483cf9b305ef--------------------------------) ·17 分钟阅读·2024 年 1 月 6 日

--

![](img/a8b71e93af85d8dfd9d788819ccea379.png)

（图片来自[Chris Ried](https://unsplash.com/@cdr6934?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/a-computer-screen-with-a-bunch-of-code-on-it-ieic5Tq8YMk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

尽管大语言模型（LLMs）已经取得了诸多成就，但驱动这些模型的基本概念依然很简单——*我们只需要准确预测下一个词*! 尽管有些人可能（合理地）认为，近期关于 LLM 的研究超越了这一基本思想，但下一个词预测依然是所有因果语言模型的预训练、微调（视变体而定）和推理过程的基础，因此它是任何 LLM 从业者必须理解的一个基本而重要的概念。

> “或许令人惊讶的是，所有这些进展的基础仍然是最初的自回归机制来生成文本，它按顺序一个一个地做出词级决策，且是从左到右的方式。” *— 引自 [10]*

在本概述中，我们将深入且实际地探讨“下一个词预测”这一概念，以理解它在语言模型训练和推理中的应用。首先，我们将在概念层面学习这些思想。然后，我们将通过一个实际实现（使用 PyTorch）来走一遍语言模型的预训练和推理过程，以便使“下一个词预测”的概念更加具体。
