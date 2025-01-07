# **Gemma**与**Llama**与**Mistral**：探索更小的人工智能模型

> 原文：[https://towardsdatascience.com/gemma-vs-llama-vs-mistral-exploring-smaller-ai-models-672a95f4b9b7?source=collection_archive---------4-----------------------#2024-08-06](https://towardsdatascience.com/gemma-vs-llama-vs-mistral-exploring-smaller-ai-models-672a95f4b9b7?source=collection_archive---------4-----------------------#2024-08-06)

## 小规模语言模型的比较研究：评估Gemma、Llama 3和Mistral在阅读理解任务中的表现

[](https://medium.com/@luisroque?source=post_page---byline--672a95f4b9b7--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--672a95f4b9b7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--672a95f4b9b7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--672a95f4b9b7--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--672a95f4b9b7--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--672a95f4b9b7--------------------------------) ·阅读时间：14分钟·2024年8月6日

--

*本文由Rafael Guedes与我共同撰写。*

# 引言

大型语言模型（LLMs）正在快速发展。每个月，新的模型都会被开发出来，以超越当前市场上的顶级模型。这种健康的竞争有利于创造出新的方法，提高质量和速度。此外，各家公司都在专注于开发更小的模型，使其能够被没有强大计算资源的个人或组织所使用。

就在几周前，苹果公司在全球开发者大会上介绍了Apple Intelligence。这是一套多种生成性模型，经过微调，旨在帮助用户撰写和优化文本、优先处理和总结通知、创作图像并执行应用内操作。苹果公司在该套件中唯一开发的基础性和专有模型也在同一场会议上推出。这是一个小型模型，设计用于在设备端运行，其中硬件成为了一个重要的限制因素。在苹果的案例中，该模型是闭源的。我们所知道的是，它是一个大约30亿参数的模型，与Gemma、Mistral和Llama 3的7b版本相当（根据苹果公司共享的结果）。
