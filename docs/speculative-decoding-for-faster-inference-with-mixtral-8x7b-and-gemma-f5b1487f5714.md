# 推测性解码：使用 Mixtral-8x7B 和 Gemma 加速推理

> 原文：[`towardsdatascience.com/speculative-decoding-for-faster-inference-with-mixtral-8x7b-and-gemma-f5b1487f5714?source=collection_archive---------4-----------------------#2024-03-08`](https://towardsdatascience.com/speculative-decoding-for-faster-inference-with-mixtral-8x7b-and-gemma-f5b1487f5714?source=collection_archive---------4-----------------------#2024-03-08)

## 使用量化模型以提高内存效率

[](https://medium.com/@bnjmn_marie?source=post_page---byline--f5b1487f5714--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--f5b1487f5714--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f5b1487f5714--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f5b1487f5714--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--f5b1487f5714--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f5b1487f5714--------------------------------) ·阅读时间：7 分钟·2024 年 3 月 8 日

--

![](img/5a5b71b90758f6c6a940f19dacf2f8cc.png)

一只推测性思考的骆驼 — 由 DALL-E 生成

更大的语言模型通常能够提供更强的性能，但以降低推理速度为代价。例如，Llama 2 70B 在下游任务中的表现远超 Llama 2 7B，但其推理速度大约慢 10 倍。

许多解码超参数的技巧和调整可以加速非常大规模语言模型（LLM）的推理。尤其是推测性解码，在许多使用场景中都非常有效。

推测性解码使用一个小型 LLM 来生成 tokens，然后由一个更强大、更大的 LLM 进行验证，或在必要时进行修正。如果小型 LLM 足够准确，推测性解码可以显著加快推理速度。

在本文中，我首先解释了推测性解码的工作原理。然后，我展示了如何使用不同的模型对，包括 Gemma、Mixtral-8x7B、Llama 2 和 Pythia，所有模型均为量化版本，来运行推测性解码。我通过基准测试推理吞吐量和内存消耗，以突出显示哪些配置效果最佳。

# 推测性解码：使用两个 LLM 进行草稿和验证
