# Neural Speed：针对4位大型语言模型的CPU快速推理

> 原文：[https://towardsdatascience.com/neural-speed-fast-inference-on-cpu-for-4-bit-large-language-models-0d611978f399?source=collection_archive---------2-----------------------#2024-04-18](https://towardsdatascience.com/neural-speed-fast-inference-on-cpu-for-4-bit-large-language-models-0d611978f399?source=collection_archive---------2-----------------------#2024-04-18)

## 比llama.cpp快最多40倍？

[](https://medium.com/@bnjmn_marie?source=post_page---byline--0d611978f399--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--0d611978f399--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0d611978f399--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0d611978f399--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--0d611978f399--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d611978f399--------------------------------) ·5分钟阅读·2024年4月18日

--

![](../Images/1517e7fb1e45ea750af91efd6cc45765.png)

使用DALL-E生成

在消费级硬件上运行大型语言模型（LLM）可能是一个挑战。如果LLM无法适应GPU内存，通常会应用量化技术来减小其大小。然而，即使经过量化，模型仍然可能过大，无法适应GPU。另一种方法是使用优化过的框架，将其在CPU RAM上运行，[例如llama.cpp](https://medium.com/@bnjmn_marie/gguf-quantization-for-fast-and-memory-efficient-inference-on-your-cpu-d10fbe58fbca)这样的CPU推理框架。

Intel也在致力于加速CPU上的推理。他们提出了一种框架，[Intel为Transformers提供的扩展](https://github.com/intel/intel-extension-for-transformers)，该框架建立在Hugging Face Transformers之上，易于使用，能够充分利用CPU。

使用[Neural Speed](https://github.com/intel/neural-speed)（Apache 2.0许可证），该框架依赖于Intel为Transformers提供的扩展，Intel进一步加速了在CPU上对4位LLM的推理。根据Intel的说法，使用此框架可以[使推理速度比llama.cpp快40倍](https://github.com/intel/neural-speed?tab=readme-ov-file#key-features)。

在本文中，我回顾了Neural Speed带来的主要优化。我展示了如何使用它并基准测试推理吞吐量。我还将其与llama.cpp进行了比较。

# Neural Speed对4位LLM的推理优化

在2023年NeurIPS大会上，Intel展示了在CPU上进行推理的主要优化：
