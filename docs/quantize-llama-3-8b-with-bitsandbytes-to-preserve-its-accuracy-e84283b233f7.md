# 使用 Bitsandbytes 对 Llama 3 8B 进行量化以保持其准确性

> 原文：[https://towardsdatascience.com/quantize-llama-3-8b-with-bitsandbytes-to-preserve-its-accuracy-e84283b233f7?source=collection_archive---------3-----------------------#2024-05-27](https://towardsdatascience.com/quantize-llama-3-8b-with-bitsandbytes-to-preserve-its-accuracy-e84283b233f7?source=collection_archive---------3-----------------------#2024-05-27)

## Llama 2 vs. Llama 3 vs. Mistral 7B，使用 GPTQ 和 Bitsandbytes 进行量化

[](https://medium.com/@bnjmn_marie?source=post_page---byline--e84283b233f7--------------------------------)[![本杰明·马里](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--e84283b233f7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e84283b233f7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e84283b233f7--------------------------------) [本杰明·马里](https://medium.com/@bnjmn_marie?source=post_page---byline--e84283b233f7--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e84283b233f7--------------------------------) ·6分钟阅读·2024年5月27日

--

![](../Images/43ed7ac2dae5a741ff7449a9bf0ea60b.png)

由 DALL-E 生成

通过量化，我们可以减小大型语言模型（LLM）的大小。量化后的 LLM 更易于在内存较小的 GPU 上运行，实际上可以作为 LLM 的压缩方法。

根据 [Meta 的自评](https://ai.meta.com/blog/meta-llama-3/)，Llama 3 8B 比 Llama 2 7B 和 Mistral 7B 更强。然而，问题仍然存在：Llama 3 8B 在量化后是否仍保持其优势？

换句话说，如果 Llama 3 比 Mistral 7B 和 Llama 2 更好（Llama 3 > Mistral 7B > Llama 2 7B），那么量化版本是否也比这些模型的量化版本更好呢（量化 Llama 3 > 量化 Mistral 7B > 量化 Llama 2 7B）？

在本文中，我们将回答这个问题。我使用 bitsandbytes 将所有模型量化为 8 位和 4 位，并使用 GPTQ 将它们量化为 8 位、4 位、3 位和 2 位，随后检查它们在 3 个不同任务上的表现。我们将看到，8 位量化在两种量化算法下对 Llama 3 都表现得相当不错。我还发现，虽然 GPTQ 4 位大大降低了模型性能，但 bitsandbytes 的量化似乎效果很好。

# Llama 3 的 GPTQ 量化
