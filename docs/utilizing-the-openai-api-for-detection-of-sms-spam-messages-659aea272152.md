# 使用 OpenAI API 检测短信垃圾信息

> 原文：[https://towardsdatascience.com/utilizing-the-openai-api-for-detection-of-sms-spam-messages-659aea272152?source=collection_archive---------5-----------------------#2024-03-15](https://towardsdatascience.com/utilizing-the-openai-api-for-detection-of-sms-spam-messages-659aea272152?source=collection_archive---------5-----------------------#2024-03-15)

## 解锁高效文本分类：使用 OpenAI 的 GPT-3.5-turbo 模型案例研究

[](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--659aea272152--------------------------------)[![Amanda Iglesias Moreno](../Images/9cff03d0c0caaa9a8aa53c4c34c90cac.png)](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--659aea272152--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--659aea272152--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--659aea272152--------------------------------) [Amanda Iglesias Moreno](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--659aea272152--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--659aea272152--------------------------------) ·6 分钟阅读·2024年3月15日

--

![](../Images/58c04d156201c9ba8045d9e596a5fb3a.png)

图片来自 [https://unsplash.com/es/@tma](https://unsplash.com/es/@tma)

传统上，任何自然语言处理文本分类项目都会从收集实例、定义各自的标签开始，然后训练一个分类模型，例如逻辑回归模型，用于对实例进行分类。目前，**OpenAI 提供的模型可以直接用于分类任务**，这些任务通常需要收集大量的标注数据来训练模型。这些预训练模型可以用于多个文本处理任务，包括分类、摘要、拼写检查和关键词识别。

我们不需要任何标注数据，也不需要训练模型。简单吧？

ChatGPT 提供了一个图形化界面，用于操作 OpenAI 实现的模型。然而，如果我们想直接在 Python 中运行这些模型怎么办呢？那就有一个可行的替代方案，那就是 OpenAI API，它允许我们从编程环境中访问他们的模型。在这篇文章中，**我们将通过一个简短的示例，介绍如何通过 API 检测短信是否为垃圾信息**。为此，我们将利用 OpenAI 的一个模型，具体来说…
