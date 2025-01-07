# 被困在网络中：图形的基础模型在哪里？

> 原文：[https://towardsdatascience.com/trapped-in-the-net-where-is-a-foundation-model-for-graphs-6154bd688d4c?source=collection_archive---------6-----------------------#2024-11-25](https://towardsdatascience.com/trapped-in-the-net-where-is-a-foundation-model-for-graphs-6154bd688d4c?source=collection_archive---------6-----------------------#2024-11-25)

## |LLM|变换器|基础模型|图|网络|

## 与其他模态断开连接的图形，等待它们的人工智能革命：它会到来吗？

[](https://salvatore-raieli.medium.com/?source=post_page---byline--6154bd688d4c--------------------------------)[![Salvatore Raieli](../Images/6bb4520e2df40d20283e7283141b5e06.png)](https://salvatore-raieli.medium.com/?source=post_page---byline--6154bd688d4c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6154bd688d4c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6154bd688d4c--------------------------------) [Salvatore Raieli](https://salvatore-raieli.medium.com/?source=post_page---byline--6154bd688d4c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6154bd688d4c--------------------------------) ·阅读时间：11分钟·2024年11月25日

--

![](../Images/e836676ce85496ae680dd31eac59e92f.png)

图片由作者使用DALL-E创建

> **“如果基础牢固，其他一切都会随之而来。”** – **未知**
> 
> **“建筑越高，基础必须越深。”** – **托马斯·阿·肯皮斯**

**基础模型近年来改变了人工智能。** 基础模型是通过大量数据（通常是无监督学习）训练出来的模型，可以适应不同的任务。像[BERT](https://it.wikipedia.org/wiki/BERT)或[GPT](https://en.wikipedia.org/wiki/Generative_pre-trained_transformer)这样的模型引发了一场革命，其中一个模型可以适应一个领域中的所有任务，从而简化了人工智能的使用，并减少了单一任务所需的数据。我们已经有了用于文本和其他模态的基础模型，但对于图形和表格数据这样的模态，目前还没有。在本文中，我们将讨论为什么我们没有图形的基础模型，以及我们如何获得一个，具体来说，我们将回答以下问题：

+   为什么我们需要图形的基础模型？为什么我们没有图形的基础模型？
