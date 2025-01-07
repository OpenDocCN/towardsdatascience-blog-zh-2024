# LLM 微调 — 常见问题解答

> 原文：[`towardsdatascience.com/llm-fine-tuning-faqs-200442827c99?source=collection_archive---------8-----------------------#2024-09-26`](https://towardsdatascience.com/llm-fine-tuning-faqs-200442827c99?source=collection_archive---------8-----------------------#2024-09-26)

## 回答我作为 AI 顾问收到的最常见问题

[](https://shawhin.medium.com/?source=post_page---byline--200442827c99--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--200442827c99--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--200442827c99--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--200442827c99--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--200442827c99--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--200442827c99--------------------------------) ·7 分钟阅读·2024 年 9 月 26 日

--

去年，我发布了一篇关于微调大型语言模型（LLMs）的文章。令我惊讶的是，这篇文章成为了我阅读量最高的博客之一，并且引发了与客户关于微调问题和 AI 项目的数十次对话。在这里，我将总结这些对话中最常见的问题以及我的回答。

![](img/eca379049c372187ac5c9bd345ce5417.png)

图片来自 Canva。

# **什么是微调？**

我喜欢将**微调**定义为**采用一个现有的（预训练的）模型，并训练至少 1 个模型参数以将其适应特定的使用场景**。

需要注意的是定义中“*训练至少 1 个模型参数*”这一部分。有些人会在定义中忽略这一细节（包括我自己有时也会忽略）。然而，这一细节将微调与诸如提示工程（prompt engineering）或前缀调优（prefix-tuning）等方法区分开来，因为后者是通过不修改模型内部操作的方式来调整模型行为。

[](/fine-tuning-large-language-models-llms-23473d763b91?source=post_page-----200442827c99--------------------------------) ## 微调大型语言模型（LLMs）

### 一个带有示例 Python 代码的概念概览

towardsdatascience.com

# **何时不应进行微调**
