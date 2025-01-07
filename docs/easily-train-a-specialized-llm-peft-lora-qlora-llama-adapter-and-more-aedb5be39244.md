# 轻松训练专门的LLM：PEFT、LoRA、QLoRA、LLaMA-Adapter等

> 原文：[https://towardsdatascience.com/easily-train-a-specialized-llm-peft-lora-qlora-llama-adapter-and-more-aedb5be39244?source=collection_archive---------2-----------------------#2024-03-09](https://towardsdatascience.com/easily-train-a-specialized-llm-peft-lora-qlora-llama-adapter-and-more-aedb5be39244?source=collection_archive---------2-----------------------#2024-03-09)

## 在自己的数据上训练一个专门的LLM比你想象的要容易…

[](https://wolfecameron.medium.com/?source=post_page---byline--aedb5be39244--------------------------------)[![Cameron R. Wolfe, Ph.D.](../Images/52bb88d7cf1105501be2fae5ccbe7a03.png)](https://wolfecameron.medium.com/?source=post_page---byline--aedb5be39244--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aedb5be39244--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aedb5be39244--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--aedb5be39244--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aedb5be39244--------------------------------) ·阅读时长31分钟·2024年3月9日

--

![](../Images/7cb5d434c1480a85e6b0d93fc4618f0c.png)

（照片由 [Clay Banks](https://unsplash.com/@claybanks?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/brown-and-white-maple-leaves-on-ground-NiYS_ExTdg8?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

由于大语言模型（LLMs）引起的兴趣激增，AI从业者常常会被问到这样的问题：*我们如何在自己的数据上训练一个专门的LLM？* 然而，回答这个问题远非易事。最近生成性AI的进展依赖于具有大量参数的巨大模型，而训练这样的LLM需要昂贵的硬件（即许多高价GPU和大量内存）和复杂的训练技术（例如，[全分片数据并行训练](https://engineering.fb.com/2021/07/15/open-source/fsdp/)）。幸运的是，这些模型通常分两个阶段进行训练——*预训练*和*微调*——其中前者阶段（通常）要贵得多。考虑到高质量的预训练LLM可以在网上轻松获得，大多数AI从业者可以简单地下载一个预训练模型，并专注于通过微调将该模型适应到他们想要的任务上。

> “微调巨大的语言模型在硬件要求和为不同任务托管独立实例的存储/切换成本方面是极其昂贵的。” *——来自[1]*

然而，模型的大小在微调期间并不会改变！因此，微调LLM——*尽管比预训练便宜*——也不是…
