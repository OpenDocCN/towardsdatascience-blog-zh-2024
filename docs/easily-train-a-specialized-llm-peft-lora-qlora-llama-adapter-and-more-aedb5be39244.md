# 轻松训练专门的 LLM：PEFT、LoRA、QLoRA、LLaMA-Adapter 等

> 原文：[`towardsdatascience.com/easily-train-a-specialized-llm-peft-lora-qlora-llama-adapter-and-more-aedb5be39244?source=collection_archive---------2-----------------------#2024-03-09`](https://towardsdatascience.com/easily-train-a-specialized-llm-peft-lora-qlora-llama-adapter-and-more-aedb5be39244?source=collection_archive---------2-----------------------#2024-03-09)

## 在自己的数据上训练一个专门的 LLM 比你想象的要容易…

[](https://wolfecameron.medium.com/?source=post_page---byline--aedb5be39244--------------------------------)![Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--aedb5be39244--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aedb5be39244--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aedb5be39244--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--aedb5be39244--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aedb5be39244--------------------------------) ·阅读时长 31 分钟·2024 年 3 月 9 日

--

![](img/7cb5d434c1480a85e6b0d93fc4618f0c.png)

（照片由 [Clay Banks](https://unsplash.com/@claybanks?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/brown-and-white-maple-leaves-on-ground-NiYS_ExTdg8?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

由于大语言模型（LLMs）引起的兴趣激增，AI 从业者常常会被问到这样的问题：*我们如何在自己的数据上训练一个专门的 LLM？* 然而，回答这个问题远非易事。最近生成性 AI 的进展依赖于具有大量参数的巨大模型，而训练这样的 LLM 需要昂贵的硬件（即许多高价 GPU 和大量内存）和复杂的训练技术（例如，[全分片数据并行训练](https://engineering.fb.com/2021/07/15/open-source/fsdp/)）。幸运的是，这些模型通常分两个阶段进行训练——*预训练*和*微调*——其中前者阶段（通常）要贵得多。考虑到高质量的预训练 LLM 可以在网上轻松获得，大多数 AI 从业者可以简单地下载一个预训练模型，并专注于通过微调将该模型适应到他们想要的任务上。

> “微调巨大的语言模型在硬件要求和为不同任务托管独立实例的存储/切换成本方面是极其昂贵的。” *——来自[1]*

然而，模型的大小在微调期间并不会改变！因此，微调 LLM——*尽管比预训练便宜*——也不是…
