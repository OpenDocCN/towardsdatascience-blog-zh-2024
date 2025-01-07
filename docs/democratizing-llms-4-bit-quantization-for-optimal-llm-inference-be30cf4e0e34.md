# 普及化LLMs：4位量化以实现最佳LLM推理

> 原文：[https://towardsdatascience.com/democratizing-llms-4-bit-quantization-for-optimal-llm-inference-be30cf4e0e34?source=collection_archive---------3-----------------------#2024-01-15](https://towardsdatascience.com/democratizing-llms-4-bit-quantization-for-optimal-llm-inference-be30cf4e0e34?source=collection_archive---------3-----------------------#2024-01-15)

## 深入探讨使用GGUF和llama.cpp进行模型量化，并使用LlamaIndex进行模型评估

[](https://medium.com/@wenqiglantz?source=post_page---byline--be30cf4e0e34--------------------------------)[![Wenqi Glantz](../Images/65b518863e01aaa48ecc6b8ac6d1be60.png)](https://medium.com/@wenqiglantz?source=post_page---byline--be30cf4e0e34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--be30cf4e0e34--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--be30cf4e0e34--------------------------------) [Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--be30cf4e0e34--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--be30cf4e0e34--------------------------------) ·阅读时间15分钟·2024年1月15日

--

![](../Images/d743e68eaf63534c03435aa893542744.png)

图像由作者使用DALL-E 3生成

对模型进行量化是一种将模型中使用的数字精度从更高精度（如32位浮点数）转换为更低精度（如4位整数）的技术。量化是在效率和准确性之间的平衡，因为它可能会以模型准确性的轻微下降为代价，因为精度的降低可能会影响模型表示数据中微妙差异的能力。

这是我从各种来源学习LLMs时的假设。

在本文中，我们将探讨将`Mistral-7B-Instruct-v0.2`模型量化为5位和4位模型的详细步骤。然后，我们将把量化后的模型上传到Hugging Face平台。最后，我们将加载这些量化后的模型，并对它们及基础模型进行评估，以了解量化对RAG流水线性能的影响。

它符合我最初的假设吗？继续阅读。

# 为什么我们要对模型进行量化？

量化模型的好处包括以下几点：

+   **减少内存使用**：更低的…
