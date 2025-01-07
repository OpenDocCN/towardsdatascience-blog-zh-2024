# 如何实现和测试 Phi3：微软强大的新一代大规模语言模型

> 原文：[`towardsdatascience.com/how-to-implement-and-test-phi3-microsofts-powerful-new-large-language-model-b2003b2aa155?source=collection_archive---------10-----------------------#2024-05-01`](https://towardsdatascience.com/how-to-implement-and-test-phi3-microsofts-powerful-new-large-language-model-b2003b2aa155?source=collection_archive---------10-----------------------#2024-05-01)

## 了解 Phi3：微软的新一代大规模语言模型，能够执行如问答和信息提取等任务

[](https://oieivind.medium.com/?source=post_page---byline--b2003b2aa155--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--b2003b2aa155--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b2003b2aa155--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2003b2aa155--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--b2003b2aa155--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2003b2aa155--------------------------------) ·12 分钟阅读·2024 年 5 月 1 日

--

本文讨论了微软新发布的 [Phi3 大规模语言模型](https://azure.microsoft.com/en-us/blog/introducing-phi-3-redefining-whats-possible-with-slms/)，这是一款能够执行多种任务的大规模语言模型，相比模型大小，具有独特的大上下文窗口。我将讨论如何在本地运行 Phi3，并进行测试以了解它在简洁回答、JSON 格式化和信息提取等任务上的表现。最后，我将分享我对该模型及其性能的看法。

![](img/ebba7aad0bfa8436146105cee31a52e1.png)

ChatGPT 可视化图像展示一个小型语言模型的辛勤工作。图像由 ChatGPT 提供。OpenAI. (2024). ChatGPT (4) [大规模语言模型]. [`chat.openai.com`](https://chat.openai.com)

# 目录

· 目录

· 动机

· 本地运行模型

· 测试模型

∘ 测试简单提示下的简洁答案

∘ 测试对象格式化能力

∘ 测试信息提取/上下文长度利用

· 我对 Phi3 的整体看法

· 结论

# 动机

撰写本文的动机是 Phi3 是微软发布的最新一代大规模语言模型之一…
