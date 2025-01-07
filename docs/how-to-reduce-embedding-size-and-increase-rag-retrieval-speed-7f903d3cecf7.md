# 如何减少嵌入大小并提高RAG检索速度

> 原文：[https://towardsdatascience.com/how-to-reduce-embedding-size-and-increase-rag-retrieval-speed-7f903d3cecf7?source=collection_archive---------2-----------------------#2024-05-26](https://towardsdatascience.com/how-to-reduce-embedding-size-and-increase-rag-retrieval-speed-7f903d3cecf7?source=collection_archive---------2-----------------------#2024-05-26)

## 使用Matryoshka表示学习（MRL）进行灵活的文本嵌入

[](https://medium.com/@leoneversberg?source=post_page---byline--7f903d3cecf7--------------------------------)[![Dr. Leon Eversberg](../Images/56dc3579a29933f7047a9ce60be4697a.png)](https://medium.com/@leoneversberg?source=post_page---byline--7f903d3cecf7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7f903d3cecf7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7f903d3cecf7--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--7f903d3cecf7--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7f903d3cecf7--------------------------------) ·阅读时间7分钟·2024年5月26日

--

![](../Images/aa83fbe893c96ba83d3975653ab8dbbd.png)

Matryoshka娃娃是大小逐渐递减的套娃。照片来自[Sandy Millar](https://unsplash.com/@sandym10?utm_source=medium&utm_medium=referral)，上传于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

文本嵌入是**单个单词或整个句子的高维向量表示**。

这些数字向量（数组）捕捉了关于基础文本的丰富信息，可以用于许多**下游任务**，例如语义理解、分类、聚类、信息检索（RAG）、重排序等。

**通常，嵌入向量的维度*d*是固定的**。嵌入维度通常是二的幂，范围从64到4096不等。

使用Matryoshka嵌入，您可以**根据应用场景更改嵌入的维度**。这可以减少存储空间，节省成本，并提高检索速度。

# 什么是文本嵌入？

我们首先定义一个**词汇表**，将所有可能的输入字符映射到整数值。词汇表不仅包括字母表中的字符，还包括特殊字符、短词和子词：

```py
{
  "a": 1,
  "b": 2,
  "c": 3,
  ...
  "z": 26,
  "the": 27,
  " ": 28
}
```
