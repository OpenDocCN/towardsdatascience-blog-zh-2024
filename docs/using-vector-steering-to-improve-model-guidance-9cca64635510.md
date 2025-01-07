# 使用向量引导来改善模型指导

> 原文：[https://towardsdatascience.com/using-vector-steering-to-improve-model-guidance-9cca64635510?source=collection_archive---------10-----------------------#2024-10-22](https://towardsdatascience.com/using-vector-steering-to-improve-model-guidance-9cca64635510?source=collection_archive---------10-----------------------#2024-10-22)

## 探索向量引导的研究并实现一个代码

[](https://medium.com/@mgunton7?source=post_page---byline--9cca64635510--------------------------------)[![Matthew Gunton](../Images/6f5a9530ad5252aa3f2fae87b3f272b1.png)](https://medium.com/@mgunton7?source=post_page---byline--9cca64635510--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9cca64635510--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9cca64635510--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--9cca64635510--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9cca64635510--------------------------------) ·阅读时间9分钟·2024年10月22日

--

![](../Images/c8147386dd5a3e8a4c0bde30076b1cb9.png)

图片来源：作者 — Flux.1

大型语言模型是复杂的，并且并不总是提供完美的答案。为了解决这个问题，人们尝试了许多不同的技术来引导模型的输出。我们见过在更大的数据集上进行预训练、使用更多参数的模型进行预训练，以及使用向量数据库（或其他查找方式）为LLM的输入添加相关的上下文。所有这些方法都能带来一定的改进，但目前没有任何一种方法是万无一失的。

引导模型的一种有趣方式是向量引导。一个有趣的例子是Claude金门大桥实验。在这个实验中，无论用户问什么，Claude都会找到一种巧妙的方式，提到它最喜欢的话题：金门大桥。

![](../Images/080af9f45153b4f3faf2149eb32e9582.png)

图片来自[“Scaling Monosemanticity: Extracting Interpretable Features from Claude 3 Sonnet”](https://transformer-circuits.pub/2024/scaling-monosemanticity/) 展示了Claude Sonnet在使用引导向量后的行为变化

今天，我将通过对这个话题的研究进行讲解，并且解释[Anastasia Borovykh的优秀代码](https://github.com/abrvkh/explainability_toolkit/blob/main/notebooks/phi3_steering_vectors.ipynb)实现。如果你对这个话题更感兴趣，我强烈推荐[查看她的视频](https://www.youtube.com/watch?v=cp-YSyc5aW8)。

让我们深入了解！

# 理论
