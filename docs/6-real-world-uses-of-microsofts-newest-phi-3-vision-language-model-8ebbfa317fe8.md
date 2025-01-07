# 微软最新 Phi-3 视觉语言模型的 6 种实际应用

> 原文：[`towardsdatascience.com/6-real-world-uses-of-microsofts-newest-phi-3-vision-language-model-8ebbfa317fe8?source=collection_archive---------0-----------------------#2024-05-24`](https://towardsdatascience.com/6-real-world-uses-of-microsofts-newest-phi-3-vision-language-model-8ebbfa317fe8?source=collection_archive---------0-----------------------#2024-05-24)

## 探索 Phi-3-Vision 的可能应用场景，这是一款小巧却强大的 MLLM，可以在本地运行（附带代码示例）

[](https://medium.com/@CVxTz?source=post_page---byline--8ebbfa317fe8--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--8ebbfa317fe8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8ebbfa317fe8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8ebbfa317fe8--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--8ebbfa317fe8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8ebbfa317fe8--------------------------------) ·阅读时间 7 分钟·2024 年 5 月 24 日

--

![](img/b74137bffcdef5766be32f53b015f9fa.png)

图片由[RoonZ nl](https://unsplash.com/@roonz_nl?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

微软最近发布了 Phi-3，一款强大的语言模型，并推出了一种新的视觉-语言变体——Phi-3-vision-128k-instruct。这款拥有 40 亿参数的模型在公开基准测试中取得了令人印象深刻的成绩，在某些情况下甚至超越了 GPT-4V，并且在除了 MMMU 之外的所有领域都优于 Gemini 1.0 Pro V。

本文将探讨如何将 Phi-3-vision-128k-instruct 作为您数据科学工具包中的强大视觉和文本模型。我们将通过各种应用场景展示其能力，包括：

+   光学字符识别（OCR）

+   图像描述

+   表格解析

+   图像理解

+   扫描文档的阅读理解

+   集合标记提示

我们将首先提供一个简单的代码片段，用于通过 transformers 和 bitsandbytes 在本地运行该模型。然后，我们将展示上述每个应用场景的示例。

## 在本地运行模型：
