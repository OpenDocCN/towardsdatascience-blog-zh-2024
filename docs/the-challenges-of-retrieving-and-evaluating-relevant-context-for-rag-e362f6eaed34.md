# 检索和评估RAG相关上下文的挑战

> 原文：[https://towardsdatascience.com/the-challenges-of-retrieving-and-evaluating-relevant-context-for-rag-e362f6eaed34?source=collection_archive---------1-----------------------#2024-06-10](https://towardsdatascience.com/the-challenges-of-retrieving-and-evaluating-relevant-context-for-rag-e362f6eaed34?source=collection_archive---------1-----------------------#2024-06-10)

## 一个案例研究，展示如何使用Ragas、TruLens和DeepEval衡量你在检索增强生成系统中上下文相关性的一年级文本理解练习

[](https://medium.com/@iamleonie?source=post_page---byline--e362f6eaed34--------------------------------)[![Leonie Monigatti](../Images/4044b1685ada53a30160b03dc78f9626.png)](https://medium.com/@iamleonie?source=post_page---byline--e362f6eaed34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e362f6eaed34--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e362f6eaed34--------------------------------) [Leonie Monigatti](https://medium.com/@iamleonie?source=post_page---byline--e362f6eaed34--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e362f6eaed34--------------------------------) ·阅读时间13分钟·2024年6月10日

--

![](../Images/988f6a45ca45f59368bb3408c8866500.png)

一年级阅读理解练习，作为检索增强生成（RAG）中的上下文相关性示例

你检索到的上下文与用户输入的相关性，在你的[检索增强生成（RAG）管道](/retrieval-augmented-generation-rag-from-theory-to-langchain-implementation-4e9bd5f6a4f2)的表现中起着关键作用。然而，检索相关上下文本身也面临着一系列挑战。更具挑战性的问题是如何有效地衡量上下文相关性。

本文将通过以下一年级文本理解示例，探讨检索相关上下文和衡量上下文相关性的挑战。

```py
text = """Lisa is at the park. Her dog Bella, is with her. 
Lisa rides her bike and plays with Bella. They race each other in the sun. 
Then Lisa goes to the pond to see the ducks. 
She thinks they are so cute and funny.
"""  

questions = [
    "Where is Lisa?",
    "What is Lisa's dog's name?",
    "What does Lisa do in the park?",
    "Why does Lisa go to the pond?"
]
```

请注意，像`gpt-3.5-turbo`这样的最先进LLM，如果将整个文本输入进去，轻松就能回答这个一年级学生的练习题……
