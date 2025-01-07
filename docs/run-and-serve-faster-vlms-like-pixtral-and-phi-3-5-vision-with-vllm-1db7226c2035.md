# 使用 vLLM 运行和提供服务给更快的 VLM，如 Pixtral 和 Phi-3.5 Vision

> 原文：[`towardsdatascience.com/run-and-serve-faster-vlms-like-pixtral-and-phi-3-5-vision-with-vllm-1db7226c2035?source=collection_archive---------4-----------------------#2024-09-23`](https://towardsdatascience.com/run-and-serve-faster-vlms-like-pixtral-and-phi-3-5-vision-with-vllm-1db7226c2035?source=collection_archive---------4-----------------------#2024-09-23)

## 了解为 VLM 提供服务所需的内存量

[](https://medium.com/@bnjmn_marie?source=post_page---byline--1db7226c2035--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--1db7226c2035--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1db7226c2035--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1db7226c2035--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--1db7226c2035--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1db7226c2035--------------------------------) ·阅读时间 10 分钟·2024 年 9 月 23 日

--

![](img/9383dc47c9e69050884d5582cbb02bb1.png)

由 Pixtral 编码的图像 — 图片来源于作者

vLLM 目前是最快的大型语言模型 (LLM) 推理引擎之一。它支持多种模型架构和量化方法。

vLLM 还支持具有多模态输入的视觉语言模型 (VLM)，这些输入包含图像和文本提示。例如，vLLM 现在可以为像 Phi-3.5 Vision 和 Pixtral 这样的模型提供服务，这些模型擅长图像描述、光学字符识别（OCR）和视觉问答（VQA）等任务。

在本文中，我将向您展示如何使用 vLLM 运行 VLM，重点介绍影响内存消耗的关键参数。我们将看到为什么 VLM 比标准 LLM 消耗更多内存。我们将以 Phi-3.5 Vision 和 Pixtral 为案例，研究一个处理包含文本和图像的提示的多模态应用。

运行 Phi-3.5 Vision 和 Pixtral 与 vLLM 的代码可以在此笔记本中找到：

[获取笔记本 (#105)](https://newsletter.kaitchup.com/p/notebooks)

# 了解 VLM 在 vLLM 中的内存消耗

在变换器模型中，按顺序生成文本一个标记一个标记非常慢，因为每个预测都依赖于所有先前的标记…
