# 小型语言模型：在 PC 和树莓派上使用 38 亿 Phi-3 和 80 亿 Llama-3 模型

> 原文：[https://towardsdatascience.com/small-language-models-using-3-8b-phi-3-and-8b-llama-3-models-on-a-pc-and-raspberry-pi-9ed70127fe61?source=collection_archive---------2-----------------------#2024-05-23](https://towardsdatascience.com/small-language-models-using-3-8b-phi-3-and-8b-llama-3-models-on-a-pc-and-raspberry-pi-9ed70127fe61?source=collection_archive---------2-----------------------#2024-05-23)

## 使用 LlamaCpp 和 ONNX 测试模型

[](https://dmitryelj.medium.com/?source=post_page---byline--9ed70127fe61--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--9ed70127fe61--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ed70127fe61--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9ed70127fe61--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--9ed70127fe61--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ed70127fe61--------------------------------) ·阅读时间 17 分钟·2024 年 5 月 23 日

--

![](../Images/e1c080c33ba6ea79ec97849e1d394374.png)

图片来自 [Jelleke Vanooteghem](https://unsplash.com/@ilumire)，Unsplash

如今，我们可以观察到在开发新的 AI 模型方面出现了有趣的变化。长期以来，人们一直知道较大的模型更“智能”，能够完成更复杂的任务。但它们的计算成本也更高。像微软、谷歌和三星这样的巨大设备制造商已经开始向客户推广新的 AI 功能，但显然，如果数百万用户在他们的手机或笔记本电脑上大规模使用 AI，计算云的成本可能会非常庞大。解决方案是什么？显而易见的方法是将模型运行在设备上，这样可以在延迟（无需网络连接，模型可以立即访问）、隐私（无需在云中处理用户响应）以及自然的计算成本方面带来优势。使用本地 AI 模型不仅对笔记本电脑和智能手机非常重要，对于自主机器人、智能家居助手和其他边缘设备也同样重要。

在撰写本文时，至少已有两个专门为设备端运行设计的模型宣布：

+   谷歌的 [Gemini Nano](https://deepmind.google/technologies/gemini/nano/)。该模型于 2023 年 12 月发布；它有 [两个版本](https://en.wikipedia.org/wiki/Gemini_(language_model))，分别为 18 亿和 32.5 亿参数。根据 [developer.android.com](https://developer.android.com/ai/aicore)...
