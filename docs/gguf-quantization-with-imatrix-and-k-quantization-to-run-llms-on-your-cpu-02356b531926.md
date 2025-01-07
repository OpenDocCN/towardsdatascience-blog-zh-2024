# 使用 Imatrix 和 K-量化进行 GGUF 量化，以便在您的 CPU 上运行 LLM

> 原文：[`towardsdatascience.com/gguf-quantization-with-imatrix-and-k-quantization-to-run-llms-on-your-cpu-02356b531926?source=collection_archive---------2-----------------------#2024-09-13`](https://towardsdatascience.com/gguf-quantization-with-imatrix-and-k-quantization-to-run-llms-on-your-cpu-02356b531926?source=collection_archive---------2-----------------------#2024-09-13)

## 为您的 CPU 提供快速且精准的 GGUF 模型

[](https://medium.com/@bnjmn_marie?source=post_page---byline--02356b531926--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--02356b531926--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--02356b531926--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--02356b531926--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--02356b531926--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--02356b531926--------------------------------) ·7 分钟阅读·2024 年 9 月 13 日

--

![](img/453132ddbe32fc254b97a076581313bd.png)

由 DALL-E 生成

GGUF 是一种二进制文件格式，旨在通过 GGML（一种基于 C 的机器学习张量库）实现高效的存储和快速的大型语言模型（LLM）加载。

GGUF 将推理所需的所有组件（包括分词器和代码）封装在一个文件中。它支持将各种语言模型转换为 GGUF 格式，例如 Llama 3、Phi 和 Qwen2。此外，它还支持将模型量化为较低的精度，以提高在 CPU 上的速度和内存效率。

我们常常写“GGUF 量化”，但 GGUF 本身只是一个文件格式，并不是一种量化方法。llama.cpp 中实现了几种量化算法，用于减少模型大小并将生成的模型序列化为 GGUF 格式。

在本文中，我们将展示如何准确地量化一个 LLM 并将其转换为 GGUF，使用重要性矩阵（imatrix）和 K-量化方法。我将提供 Gemma 2 Instruct 的 GGUF 转换代码，使用了 imatrix。它与其他由 llama.cpp 支持的模型一样有效：Qwen2、Llama 3、Phi-3 等。我们还将讨论如何评估量化的准确性以及生成模型的推理吞吐量。
