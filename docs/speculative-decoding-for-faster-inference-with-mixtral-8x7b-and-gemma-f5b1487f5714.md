# 推测性解码：使用Mixtral-8x7B和Gemma加速推理

> 原文：[https://towardsdatascience.com/speculative-decoding-for-faster-inference-with-mixtral-8x7b-and-gemma-f5b1487f5714?source=collection_archive---------4-----------------------#2024-03-08](https://towardsdatascience.com/speculative-decoding-for-faster-inference-with-mixtral-8x7b-and-gemma-f5b1487f5714?source=collection_archive---------4-----------------------#2024-03-08)

## 使用量化模型以提高内存效率

[](https://medium.com/@bnjmn_marie?source=post_page---byline--f5b1487f5714--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--f5b1487f5714--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f5b1487f5714--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f5b1487f5714--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--f5b1487f5714--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f5b1487f5714--------------------------------) ·阅读时间：7分钟·2024年3月8日

--

![](../Images/5a5b71b90758f6c6a940f19dacf2f8cc.png)

一只推测性思考的骆驼 — 由DALL-E生成

更大的语言模型通常能够提供更强的性能，但以降低推理速度为代价。例如，Llama 2 70B在下游任务中的表现远超Llama 2 7B，但其推理速度大约慢10倍。

许多解码超参数的技巧和调整可以加速非常大规模语言模型（LLM）的推理。尤其是推测性解码，在许多使用场景中都非常有效。

推测性解码使用一个小型LLM来生成tokens，然后由一个更强大、更大的LLM进行验证，或在必要时进行修正。如果小型LLM足够准确，推测性解码可以显著加快推理速度。

在本文中，我首先解释了推测性解码的工作原理。然后，我展示了如何使用不同的模型对，包括Gemma、Mixtral-8x7B、Llama 2和Pythia，所有模型均为量化版本，来运行推测性解码。我通过基准测试推理吞吐量和内存消耗，以突出显示哪些配置效果最佳。

# 推测性解码：使用两个LLM进行草稿和验证
