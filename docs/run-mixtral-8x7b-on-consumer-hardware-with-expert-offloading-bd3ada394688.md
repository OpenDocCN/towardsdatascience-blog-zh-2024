# 在消费级硬件上运行Mixtral-8x7B并进行专家卸载

> 原文：[https://towardsdatascience.com/run-mixtral-8x7b-on-consumer-hardware-with-expert-offloading-bd3ada394688?source=collection_archive---------1-----------------------#2024-01-11](https://towardsdatascience.com/run-mixtral-8x7b-on-consumer-hardware-with-expert-offloading-bd3ada394688?source=collection_archive---------1-----------------------#2024-01-11)

## 寻找内存使用和推理速度之间的最佳平衡

[](https://medium.com/@bnjmn_marie?source=post_page---byline--bd3ada394688--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--bd3ada394688--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bd3ada394688--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bd3ada394688--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--bd3ada394688--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bd3ada394688--------------------------------) ·阅读时长8分钟·2024年1月11日

--

![](../Images/0878942c81ee58eeef1c04a5a585e7d0.png)

Mixtral-8x7B专家子网络的激活模式 — [来源](https://arxiv.org/pdf/2312.17238.pdf) (CC-BY)

虽然Mixtral-8x7B是最优秀的开源大型语言模型（LLM）之一，但它也是一个庞大的模型，拥有46.7亿个参数。即使将模型量化到4位，它也无法完全加载到消费级GPU中（例如，具有24GB VRAM的RTX 3090也不足够）。

Mixtral-8x7B是一个[专家混合模型（MoE）](https://kaitchup.substack.com/p/mixtral-8x7b-understanding-and-running)，由8个每个拥有60亿参数的专家子网络组成。

由于在解码过程中只有8个专家中的2个是有效的，剩余的6个专家可以被移动或*卸载*到另一个设备，例如CPU RAM，以释放一些GPU VRAM。在实际操作中，这种卸载是复杂的。

选择激活哪个专家是一个在推理时针对每个输入标记和每一层模型所做的决定。像[Accelerate的device_map](https://kaitchup.substack.com/p/device-map-avoid-out-of-memory-errors-when-running-large-language-models-af7de5076f9d)那样简单地将部分模型转移到CPU RAM，会在CPU和GPU之间产生通信瓶颈。

[Mixtral-offloading](https://github.com/dvmazur/mixtral-offloading)（MIT许可）是一个提出更高效解决方案的项目，旨在减少VRAM的消耗，同时保持合理的推理速度。
