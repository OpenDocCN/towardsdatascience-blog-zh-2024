# LLMs 中的提示缓存：直觉

> 原文：[https://towardsdatascience.com/prompt-caching-in-llms-intuition-5cfc151c4420?source=collection_archive---------4-----------------------#2024-10-04](https://towardsdatascience.com/prompt-caching-in-llms-intuition-5cfc151c4420?source=collection_archive---------4-----------------------#2024-10-04)

## 介绍缓存在基于注意力的模型中的工作原理

[](https://medium.com/@rodrigonader?source=post_page---byline--5cfc151c4420--------------------------------)[![Rodrigo Nader](../Images/c1715d46ef7939ff85fc7c908e92b2f1.png)](https://medium.com/@rodrigonader?source=post_page---byline--5cfc151c4420--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5cfc151c4420--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5cfc151c4420--------------------------------) [Rodrigo Nader](https://medium.com/@rodrigonader?source=post_page---byline--5cfc151c4420--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5cfc151c4420--------------------------------) ·阅读时间 4 分钟·2024年10月4日

--

![](../Images/9da5fcfecad7b9e5ffa7ce5a6172ef0b.png)

图片由作者使用 ChatGPT 制作

我一直在探索关于*提示缓存*如何工作的文章，虽然有一些博客涉及到它的实用性以及如何实现，但我还没有找到太多关于其实际机制或背后直觉的内容。

问题归根结底是：类似 GPT 的模型生成依赖于**提示中每个标记之间的关系**。*如何仅缓存部分提示内容才能有意义呢？*

出人意料的是，这确实是可行的。让我们深入探讨一下吧！

**提示缓存**最近作为一种重要的技术进展出现，能够减少计算开销、延迟和成本，尤其是对于那些频繁重用提示片段的应用程序。

为了澄清，这些情况是指你有一个长且静态的预提示（上下文），并不断添加新的用户问题。每次调用 API 模型时，它需要完全重新处理**整个提示**。

**谷歌**是第一个推出[**上下文缓存**](https://cloud.google.com/vertex-ai/generative-ai/docs/context-cache/context-cache-overview)的公司，使用的是 Gemini 模型，而[**Anthropic**](https://www.anthropic.com/news/prompt-caching)和[**OpenAI**](https://platform.openai.com/docs/guides/prompt-caching)最近也集成了他们的提示缓存功能，声称能够大幅降低长提示的成本和延迟。

# 什么是提示缓存？

提示缓存是一种技术，它存储提示的部分内容（如系统消息、文档或模板文本），以便高效地重用。这避免了重复处理相同的提示结构，从而提高效率。

实现提示缓存的方式有多种，因此不同提供商可能采用不同的技术，但我们将尝试从两种流行方法中提炼出该概念：

+   [**GPTCache：大语言模型的语义缓存**](https://aclanthology.org/2023.nlposs-1.24.pdf)

+   [**提示缓存：模块化注意力重用**](https://arxiv.org/pdf/2311.04934)

整体过程如下：

1.  当提示输入时，它会经历标记化、向量化和完整的模型推理（通常是大语言模型的注意力模型）。

1.  系统将相关数据（令牌及其嵌入向量）存储在**缓存层**，该缓存层位于模型之外。令牌的数值向量表示被存储在内存中。

1.  在下一次调用时，系统检查新提示的一部分是否已存储在缓存中（例如，基于嵌入相似性）。

1.  在缓存命中时，缓存的部分被检索，跳过了标记化和完整模型推理的过程。

![](../Images/e7f90c2a611b4e1abbbfd64dac73e832.png)

[https://aclanthology.org/2023.nlposs-1.24.pdf](https://aclanthology.org/2023.nlposs-1.24.pdf)

# 那么……到底是什么被缓存了呢？

在最基本的形式下，根据方法的不同，可以应用不同级别的缓存，从简单到复杂不等。这可以包括存储令牌、令牌嵌入，甚至内部状态，以避免重新处理：

+   **令牌**：下一级涉及缓存提示的**标记化表示**，避免了对重复输入的重新标记化。

+   **令牌编码**：缓存这些可以让模型跳过**重新编码**之前看到的输入，仅处理提示中的*新*部分。

+   **内部状态**：在最复杂的层级中，缓存内部状态，如键值对（见下文），存储了**令牌之间的关系**，因此模型只计算*新的*关系。

# 缓存键值状态

在变换器模型中，令牌是成对处理的：**键**和**值**。

+   **键**帮助模型决定每个令牌应给予其他令牌多少重要性或“注意力”。

+   **值**表示令牌在上下文中的实际内容或意义。

例如，在句子*“哈利·波特是个巫师，他的朋友是罗恩。”*中，*“哈利”*的键是一个向量，表示与句子中每个其他词的关系：

`["Harry", "Potter"], ["Harry", "a"], ["Harry", "wizard"]，等等…`

# KV提示缓存如何工作

1.  **预计算并缓存KV状态**：模型计算并存储常用提示的KV对，允许跳过重新计算，从缓存中检索这些对，以提高效率。

1.  **合并缓存和新上下文**：在新的提示中，模型检索之前使用过的句子的缓存KV对，同时计算任何新句子的KV对。

1.  **跨句KV计算**：模型计算新的KV对，将缓存的令牌从一个句子链接到另一个句子的新的令牌，从而实现对它们关系的整体理解。

![](../Images/46f1763f48212864ed71f0fe6cb78be1.png)

[https://arxiv.org/abs/2311.04934](https://arxiv.org/abs/2311.04934)

**总结：**

> 缓存的提示中，所有标记之间的关系已经计算完毕。只有NEW-OLD或NEW-NEW标记之间的新关系需要重新计算。

# 这意味着RAG的终结吗？

随着模型上下文大小的增加，提示缓存将通过避免重复处理带来很大的差异。因此，一些人可能倾向于只使用庞大的提示，并完全跳过检索过程。

但问题是：随着上下文的增长，模型会失去焦点。这不是因为模型表现不佳，而是因为在一大块数据中寻找答案是一个主观任务，取决于具体的使用案例需求。

能够存储和管理大量向量的系统仍然至关重要，而RAG通过提供一些关键功能，超越了缓存提示：控制。

使用RAG，你可以从数据中筛选并仅检索最相关的部分，而不必依赖模型处理所有内容。模块化、分离的方法减少了噪音，比起全上下文输入，它能为你提供更多的透明度和精确度。

最后，出现的更大上下文模型可能会要求更好的提示向量存储，而不是简单的缓存。这是否意味着我们回到了……向量存储？

在Langflow，我们正在打造从RAG原型到生产的最快路径。它是开源的，并且提供免费的云服务！快来看看 [https://github.com/langflow-ai/langflow](https://github.com/langflow-ai/langflow) ✨
