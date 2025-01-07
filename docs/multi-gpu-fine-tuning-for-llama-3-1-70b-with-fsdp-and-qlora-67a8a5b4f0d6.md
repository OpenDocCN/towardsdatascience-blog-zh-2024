# 多 GPU 微调 Llama 3.1 70B 模型，采用 FSDP 和 QLoRA

> 原文：[`towardsdatascience.com/multi-gpu-fine-tuning-for-llama-3-1-70b-with-fsdp-and-qlora-67a8a5b4f0d6?source=collection_archive---------1-----------------------#2024-08-08`](https://towardsdatascience.com/multi-gpu-fine-tuning-for-llama-3-1-70b-with-fsdp-and-qlora-67a8a5b4f0d6?source=collection_archive---------1-----------------------#2024-08-08)

## 使用仅有的 2 块 24 GB GPU 和大量 CPU 内存，你可以做什么

[](https://medium.com/@bnjmn_marie?source=post_page---byline--67a8a5b4f0d6--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--67a8a5b4f0d6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--67a8a5b4f0d6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--67a8a5b4f0d6--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--67a8a5b4f0d6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--67a8a5b4f0d6--------------------------------) ·8 分钟阅读·2024 年 8 月 8 日

--

![](img/d64c5415cadb5ca8010b4aa2d818a1f8.png)

使用 DALL-E 生成

微调最多 35B 参数的大型语言模型（LLMs）相对简单且廉价，因为只需要一块消费级 GPU 即可完成。理论上，使用单块消费级 GPU 微调更大的模型并非不可能，因为我们可以将模型的部分内容转移到 CPU 内存中。然而，即使使用高端 CPU，这个过程将会极其缓慢。

使用多个 GPU 是保持微调速度足够快的唯一替代方案。配置为 2 块 24 GB GPU 的系统提供了很多可能性。48 GB 的 GPU 内存足以微调 70B 模型，如 Llama 3 70B 和 Qwen2 72B。

在本文中，我将解释如何仅使用两块 GPU，通过 FSDP 和 QLoRA 微调 70B 参数的 LLM。

我首先解释什么是 FSDP，然后我们将看到如何修改标准的 QLoRA 微调代码，以便在多个 GPU 上运行。在实验和演示中，我使用了 Llama 3.1 70B，但对其他 LLM 也能起到类似的效果。硬件方面，我依赖于[RunPod (推荐链接)](https://runpod.io/?ref=1ip9lvtj)提供的 2 块 RTX 3090 GPU。使用 2 块 RTX 4090 GPU 会更快，但成本也更高。

我还制作了一个笔记本，实施了本文中描述的代码。它可以在这里获取：
