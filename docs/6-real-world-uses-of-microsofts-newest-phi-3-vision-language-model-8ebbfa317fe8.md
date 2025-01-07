# 微软最新Phi-3视觉语言模型的6种实际应用

> 原文：[https://towardsdatascience.com/6-real-world-uses-of-microsofts-newest-phi-3-vision-language-model-8ebbfa317fe8?source=collection_archive---------0-----------------------#2024-05-24](https://towardsdatascience.com/6-real-world-uses-of-microsofts-newest-phi-3-vision-language-model-8ebbfa317fe8?source=collection_archive---------0-----------------------#2024-05-24)

## 探索Phi-3-Vision的可能应用场景，这是一款小巧却强大的MLLM，可以在本地运行（附带代码示例）

[](https://medium.com/@CVxTz?source=post_page---byline--8ebbfa317fe8--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--8ebbfa317fe8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8ebbfa317fe8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8ebbfa317fe8--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--8ebbfa317fe8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8ebbfa317fe8--------------------------------) ·阅读时间7分钟·2024年5月24日

--

![](../Images/b74137bffcdef5766be32f53b015f9fa.png)

图片由[RoonZ nl](https://unsplash.com/@roonz_nl?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

微软最近发布了Phi-3，一款强大的语言模型，并推出了一种新的视觉-语言变体——Phi-3-vision-128k-instruct。这款拥有40亿参数的模型在公开基准测试中取得了令人印象深刻的成绩，在某些情况下甚至超越了GPT-4V，并且在除了MMMU之外的所有领域都优于Gemini 1.0 Pro V。

本文将探讨如何将Phi-3-vision-128k-instruct作为您数据科学工具包中的强大视觉和文本模型。我们将通过各种应用场景展示其能力，包括：

+   光学字符识别（OCR）

+   图像描述

+   表格解析

+   图像理解

+   扫描文档的阅读理解

+   集合标记提示

我们将首先提供一个简单的代码片段，用于通过transformers和bitsandbytes在本地运行该模型。然后，我们将展示上述每个应用场景的示例。

## 在本地运行模型：
