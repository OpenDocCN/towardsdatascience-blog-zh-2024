# 使用 vLLM 同时服务多个 LoRA 适配器

> 原文：[`towardsdatascience.com/serve-multiple-lora-adapters-with-vllm-5323b0425b82?source=collection_archive---------6-----------------------#2024-08-03`](https://towardsdatascience.com/serve-multiple-lora-adapters-with-vllm-5323b0425b82?source=collection_archive---------6-----------------------#2024-08-03)

## 不增加延迟

[](https://medium.com/@bnjmn_marie?source=post_page---byline--5323b0425b82--------------------------------)![本杰明·玛丽](https://medium.com/@bnjmn_marie?source=post_page---byline--5323b0425b82--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5323b0425b82--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5323b0425b82--------------------------------) [本杰明·玛丽](https://medium.com/@bnjmn_marie?source=post_page---byline--5323b0425b82--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5323b0425b82--------------------------------) ·阅读时间 6 分钟·2024 年 8 月 3 日

--

![](img/49568640e65f99e6f16e6765da72f2a4.png)

由 DALL-E 生成

使用 LoRA 适配器时，我们可以将大语言模型（LLM）专门化为某个任务或领域。适配器必须加载到 LLM 上，才能用于推理。在某些应用中，为用户提供多个适配器可能是有用的。例如，一个适配器可以执行函数调用，另一个适配器可以执行完全不同的任务，如分类、翻译或其他语言生成任务。

然而，要使用多个适配器，一个标准的推理框架首先必须卸载当前适配器，然后加载新适配器。这一卸载/加载过程可能需要几秒钟，可能会影响用户体验。

幸运的是，有一些开源框架能够同时服务多个适配器，而不会在使用两个不同适配器之间产生明显的延迟。例如，[vLLM](https://github.com/vllm-project/vllm)（Apache 2.0 许可），是最高效的开源推理框架之一，可以轻松地同时运行和服务多个 LoRA 适配器。

在本文中，我们将展示如何使用 vLLM 配合多个 LoRA 适配器。我将解释如何在离线推理中使用 LoRA 适配器，以及如何为用户提供多个适配器以进行在线推理。我使用 Llama 3 作为示例，展示适配器在函数调用和对话中的应用。
