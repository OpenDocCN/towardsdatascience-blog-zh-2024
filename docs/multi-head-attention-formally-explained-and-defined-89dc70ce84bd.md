# 多头注意力 — 形式化解释与定义

> 原文：[`towardsdatascience.com/multi-head-attention-formally-explained-and-defined-89dc70ce84bd?source=collection_archive---------9-----------------------#2024-06-11`](https://towardsdatascience.com/multi-head-attention-formally-explained-and-defined-89dc70ce84bd?source=collection_archive---------9-----------------------#2024-06-11)

## 多头注意力的全面且详细的形式化

[](https://medium.com/@jmpion?source=post_page---byline--89dc70ce84bd--------------------------------)![Jean Meunier-Pion](https://medium.com/@jmpion?source=post_page---byline--89dc70ce84bd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--89dc70ce84bd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--89dc70ce84bd--------------------------------) [Jean Meunier-Pion](https://medium.com/@jmpion?source=post_page---byline--89dc70ce84bd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--89dc70ce84bd--------------------------------) ·阅读时长 9 分钟·2024 年 6 月 11 日

--

![](img/06dd5a838c54bd29e7e3bcf3512bced8.png)

带有多个头部的机器人，正在集中注意力 — 作者提供的图像（由 AI 生成，Microsoft Copilot）

多头注意力在变换器（Transformers）中扮演着至关重要的角色，而变换器已经彻底改变了自然语言处理（NLP）。理解这个机制是了解当前最先进语言模型的必要步骤。

尽管这个概念几年前已经提出，并且此后被广泛使用和讨论，但模糊的符号表示和缺乏正式定义，导致新手难以迅速揭开多头注意力机制的谜团。

本文的目标是提供一个全面且清晰的多头注意力形式化定义，使这一机制更容易理解。

由于要更好地理解新概念，关键是要自己动手实践，本文附带了几个练习/问题（*以及解答*），以帮助你准确理解多头注意力机制的作用。

**免责声明：** 在开始多头注意力的定义和解释之前，请注意，由于缺乏对 LaTeX 的支持，我不得不将方程转化为图像，以显示不同的数学对象。

# 输入
