# 从零开始学习 LLMs 和 Transformer：解码器

> 原文：[https://towardsdatascience.com/llms-and-transformers-from-scratch-the-decoder-d533008629c5?source=collection_archive---------2-----------------------#2024-01-10](https://towardsdatascience.com/llms-and-transformers-from-scratch-the-decoder-d533008629c5?source=collection_archive---------2-----------------------#2024-01-10)

## 探索 Transformer 的解码器架构：掩蔽的多头注意力、编码器-解码器注意力以及实际实现

[](https://medium.com/@luisroque?source=post_page---byline--d533008629c5--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--d533008629c5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d533008629c5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d533008629c5--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--d533008629c5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d533008629c5--------------------------------) ·13分钟阅读·2024年1月10日

--

*本文由 Rafael Nardi 合著。*

# 介绍

在本文中，我们深入探讨了 Transformer 架构中的解码器组件，重点分析它与编码器的异同。解码器的独特特点在于其类似循环的迭代特性，这与编码器的线性处理方式形成鲜明对比。解码器的核心是两种修改过的注意力机制：掩蔽的多头注意力和编码器-解码器多头注意力。

解码器中的掩蔽多头注意力确保了令牌的顺序处理，这种方法防止了每个生成的令牌受到后续令牌的影响。这种掩蔽对于保持生成数据的顺序性和连贯性至关重要。解码器输出（来自掩蔽注意力）与编码器输出之间的交互体现在编码器-解码器注意力中。最后一步将输入上下文引入解码器的处理过程中。

我们还将演示如何使用 Python 和 NumPy 实现这些概念。我们创建了一个简单的示例，用于将一句话从英语翻译成葡萄牙语。这种实用的方法将帮助阐明 Transformer 模型中解码器的内部工作原理……
