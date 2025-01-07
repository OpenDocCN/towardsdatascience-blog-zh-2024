# NLP插图，第2部分：词嵌入

> 原文：[https://towardsdatascience.com/nlp-illustrated-part-2-word-embeddings-6d718ac40b7d?source=collection_archive---------5-----------------------#2024-11-27](https://towardsdatascience.com/nlp-illustrated-part-2-word-embeddings-6d718ac40b7d?source=collection_archive---------5-----------------------#2024-11-27)

## 一本关于词嵌入的插图和直观指南

[](https://medium.com/@shreya.rao?source=post_page---byline--6d718ac40b7d--------------------------------)[![Shreya Rao](../Images/03f13be6f5f67783d32f0798f09a4f86.png)](https://medium.com/@shreya.rao?source=post_page---byline--6d718ac40b7d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6d718ac40b7d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6d718ac40b7d--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--6d718ac40b7d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6d718ac40b7d--------------------------------) ·阅读时间：8分钟·2024年11月27日

--

欢迎来到我们NLP系列的第2部分。如果你已经阅读过[第1部分](https://medium.com/towards-data-science/nlp-illustrated-part-1-text-encoding-41ba06c0f512)，你会记得我们正在解决的挑战是将文本转换为数字，以便将其输入到我们的机器学习模型或神经网络中。

[](/nlp-illustrated-part-1-text-encoding-41ba06c0f512?source=post_page-----6d718ac40b7d--------------------------------) [## NLP插图，第1部分：文本编码

### 一本关于文本到数字转换的插图指南，包含代码

towardsdatascience.com](/nlp-illustrated-part-1-text-encoding-41ba06c0f512?source=post_page-----6d718ac40b7d--------------------------------)

之前，我们探讨了一些基本的（也非常初步的）方法，如词袋模型和TF-IDF。虽然这些方法能够完成任务，但我们也看到了它们的局限性——主要是它们无法捕捉单词的深层含义或单词之间的关系。

这就是**词嵌入**发挥作用的地方。它们提供了一种更智能的方式来将文本表示为数字，不仅捕捉了单词本身，还能够表达它们的含义和上下文。

让我们通过一个简单的类比来分解这个概念，使它变得非常直观。

**假设我们想将电影表示为数字**。以电影[*利刃出鞘*](https://www.imdb.com/title/tt8946378/?ref_=tt_mv_close)为例。

![](../Images/8ea3be3d4c56a98941190ebf0e83665d.png)

来源：[维基百科](https://en.wikipedia.org/wiki/Knives_Out#/media/File:Knives_Out_poster.jpeg)

我们可以通过在不同特征上对电影进行评分来用数字表示一部电影，比如…
