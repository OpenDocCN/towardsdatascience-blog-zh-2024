# 朝向无限LLM上下文窗口

> 原文：[https://towardsdatascience.com/towards-infinite-llm-context-windows-e099225abaaf?source=collection_archive---------0-----------------------#2024-04-28](https://towardsdatascience.com/towards-infinite-llm-context-windows-e099225abaaf?source=collection_archive---------0-----------------------#2024-04-28)

## 一切始于GPT具有512个标记的输入上下文窗口。仅仅五年后，最新的LLM已经能够处理超过100万个上下文输入。那么，极限在哪里呢？

[](https://medium.com/@krzysztof.kornel?source=post_page---byline--e099225abaaf--------------------------------)[![Krzysztof K. Zdeb](../Images/4531b37707bf6a01ef635e4b9ecfc03f.png)](https://medium.com/@krzysztof.kornel?source=post_page---byline--e099225abaaf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e099225abaaf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e099225abaaf--------------------------------) [Krzysztof K. Zdeb](https://medium.com/@krzysztof.kornel?source=post_page---byline--e099225abaaf--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e099225abaaf--------------------------------) ·阅读时间9分钟·2024年4月28日

--

![](../Images/991696e8052dfe143c32539a274be31a.png)

我喜欢将**LLMs**（具体来说，是模型的参数，即神经网络层的权重和注意力机制的权重）看作是一个庞大的记忆库，里面包含了关于世界的各种信息，并且混合了如何处理文本的语言知识。这就像是模型在预训练过程中学习到的静态知识，（可选地）经过微调后，形成了一个可以在所有与模型的对话中重用的公共核心，无论是谁提出问题。另一方面，**输入上下文**则是魔法发生的地方。它就像是向模型传递一条带有特殊指令或信息的便条。这可以是我们希望模型回答的问题，也可以是我们拥有的独特信息，甚至是我们与模型之前对话的历史。这是一种给模型提供个人记忆的方式。

# 上下文大小的演变

一个LLM的关键特性之一是它的输入上下文大小。它可以定义为语言模型在生成时考虑的单词数量（或者更准确地说，是[**标记**](https://help.openai.com/en/articles/4936856-what-are-tokens-and-how-to-count-them)）。  
