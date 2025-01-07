# 使用Imatrix和K-量化进行GGUF量化，以便在您的CPU上运行LLM

> 原文：[https://towardsdatascience.com/gguf-quantization-with-imatrix-and-k-quantization-to-run-llms-on-your-cpu-02356b531926?source=collection_archive---------2-----------------------#2024-09-13](https://towardsdatascience.com/gguf-quantization-with-imatrix-and-k-quantization-to-run-llms-on-your-cpu-02356b531926?source=collection_archive---------2-----------------------#2024-09-13)

## 为您的CPU提供快速且精准的GGUF模型

[](https://medium.com/@bnjmn_marie?source=post_page---byline--02356b531926--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--02356b531926--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--02356b531926--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--02356b531926--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--02356b531926--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--02356b531926--------------------------------) ·7分钟阅读·2024年9月13日

--

![](../Images/453132ddbe32fc254b97a076581313bd.png)

由DALL-E生成

GGUF是一种二进制文件格式，旨在通过GGML（一种基于C的机器学习张量库）实现高效的存储和快速的大型语言模型（LLM）加载。

GGUF将推理所需的所有组件（包括分词器和代码）封装在一个文件中。它支持将各种语言模型转换为GGUF格式，例如Llama 3、Phi和Qwen2。此外，它还支持将模型量化为较低的精度，以提高在CPU上的速度和内存效率。

我们常常写“GGUF量化”，但GGUF本身只是一个文件格式，并不是一种量化方法。llama.cpp中实现了几种量化算法，用于减少模型大小并将生成的模型序列化为GGUF格式。

在本文中，我们将展示如何准确地量化一个LLM并将其转换为GGUF，使用重要性矩阵（imatrix）和K-量化方法。我将提供Gemma 2 Instruct的GGUF转换代码，使用了imatrix。它与其他由llama.cpp支持的模型一样有效：Qwen2、Llama 3、Phi-3等。我们还将讨论如何评估量化的准确性以及生成模型的推理吞吐量。
