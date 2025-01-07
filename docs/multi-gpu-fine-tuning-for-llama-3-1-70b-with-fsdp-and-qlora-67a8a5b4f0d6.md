# 多GPU微调Llama 3.1 70B模型，采用FSDP和QLoRA

> 原文：[https://towardsdatascience.com/multi-gpu-fine-tuning-for-llama-3-1-70b-with-fsdp-and-qlora-67a8a5b4f0d6?source=collection_archive---------1-----------------------#2024-08-08](https://towardsdatascience.com/multi-gpu-fine-tuning-for-llama-3-1-70b-with-fsdp-and-qlora-67a8a5b4f0d6?source=collection_archive---------1-----------------------#2024-08-08)

## 使用仅有的2块24 GB GPU和大量CPU内存，你可以做什么

[](https://medium.com/@bnjmn_marie?source=post_page---byline--67a8a5b4f0d6--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--67a8a5b4f0d6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--67a8a5b4f0d6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--67a8a5b4f0d6--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--67a8a5b4f0d6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--67a8a5b4f0d6--------------------------------) ·8分钟阅读·2024年8月8日

--

![](../Images/d64c5415cadb5ca8010b4aa2d818a1f8.png)

使用DALL-E生成

微调最多35B参数的大型语言模型（LLMs）相对简单且廉价，因为只需要一块消费级GPU即可完成。理论上，使用单块消费级GPU微调更大的模型并非不可能，因为我们可以将模型的部分内容转移到CPU内存中。然而，即使使用高端CPU，这个过程将会极其缓慢。

使用多个GPU是保持微调速度足够快的唯一替代方案。配置为2块24 GB GPU的系统提供了很多可能性。48 GB的GPU内存足以微调70B模型，如Llama 3 70B和Qwen2 72B。

在本文中，我将解释如何仅使用两块GPU，通过FSDP和[QLoRA](/qlora-fine-tune-a-large-language-model-on-your-gpu-27bed5a03e2b)微调70B参数的LLM。

我首先解释什么是FSDP，然后我们将看到如何修改标准的QLoRA微调代码，以便在多个GPU上运行。在实验和演示中，我使用了Llama 3.1 70B，但对其他LLM也能起到类似的效果。硬件方面，我依赖于[RunPod (推荐链接)](https://runpod.io/?ref=1ip9lvtj)提供的2块RTX 3090 GPU。使用2块RTX 4090 GPU会更快，但成本也更高。

我还制作了一个笔记本，实施了本文中描述的代码。它可以在这里获取：
