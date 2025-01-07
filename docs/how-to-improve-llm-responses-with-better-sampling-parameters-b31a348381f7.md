# 如何通过更好的采样参数改进LLM的响应

> 原文：[https://towardsdatascience.com/how-to-improve-llm-responses-with-better-sampling-parameters-b31a348381f7?source=collection_archive---------2-----------------------#2024-09-02](https://towardsdatascience.com/how-to-improve-llm-responses-with-better-sampling-parameters-b31a348381f7?source=collection_archive---------2-----------------------#2024-09-02)

## 深入探讨温度、top_p、top_k和min_p的随机解码

[](https://medium.com/@leoneversberg?source=post_page---byline--b31a348381f7--------------------------------)[![Dr. Leon Eversberg](../Images/56dc3579a29933f7047a9ce60be4697a.png)](https://medium.com/@leoneversberg?source=post_page---byline--b31a348381f7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b31a348381f7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b31a348381f7--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--b31a348381f7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b31a348381f7--------------------------------) ·阅读时间10分钟·2024年9月2日

--

![](../Images/a757570a7c6383a8115db038505b49a0.png)

在使用Python SDK调用OpenAI API时，你是否曾经想过`temperature`和`top_p`参数到底是做什么的？

当你向大型语言模型（LLM）提问时，模型会为其词汇表中的每个可能令牌输出一个概率。

在从这个概率分布中采样一个令牌后，我们可以将选定的令牌附加到输入提示中，以便大型语言模型（LLM）可以输出下一个令牌的概率。

这个采样过程可以通过如著名的`temperature`（温度）和`top_p`等参数进行控制。

在这篇文章中，我将解释并可视化定义LLM输出行为的采样策略。通过理解这些参数的作用并根据我们的使用案例设置它们，我们可以改进LLM生成的输出。

对于本文，我将使用[VLLM](https://github.com/vllm-project/vllm)作为推理引擎，并使用微软的新模型[Phi-3.5-mini-instruct](https://huggingface.co/microsoft/Phi-3.5-mini-instruct)，该模型采用AWQ量化技术。为了在本地运行这个模型，我使用了我笔记本电脑的NVIDIA GeForce RTX 2060 GPU。

## 目录

· [理解带Logprobs的采样](#6e79)

∘ [LLM 解码理论](#082f)

∘ [使用OpenAI Python SDK获取Logprobs](#fcbc)

· [贪心解码](#798b)

· [温度](#7d10)

· [Top-k 采样](#0110)

· [Top-p 采样](#52d3)

· [组合 Top-p](#c163)…
