# TARNet 和 Dragonnet：S-学习者与 T-学习者之间的因果推断

> 原文：[https://towardsdatascience.com/tarnet-and-dragonnet-causal-inference-between-s-and-t-learners-0444b8cc65bd?source=collection_archive---------6-----------------------#2024-03-05](https://towardsdatascience.com/tarnet-and-dragonnet-causal-inference-between-s-and-t-learners-0444b8cc65bd?source=collection_archive---------6-----------------------#2024-03-05)

## 学习如何构建用于直接因果推断的神经网络

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--0444b8cc65bd--------------------------------)[![Dr. Robert Kübler](../Images/3b8d8b88f76c0c43d9c305e3885e7ab9.png)](https://dr-robert-kuebler.medium.com/?source=post_page---byline--0444b8cc65bd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0444b8cc65bd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0444b8cc65bd--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--0444b8cc65bd--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0444b8cc65bd--------------------------------) ·6分钟阅读·2024年3月5日

--

![](../Images/e81bd91cee0a16385afdc4e36f564be4.png)

图片来源：[Geranimo](https://unsplash.com/@geraninmo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如今，构建机器学习模型已经变得相当简单，但往往，做出准确的预测并不足够。更进一步，我们希望能够做出关于**干预措施**的**因果声明**。知道一个客户将会离开我们的公司是好事，但知道该怎么做——例如，发送一张优惠券——要好得多。这需要更多的工作，我在另一篇文章中已经解释了基本概念。

[](/easy-methods-for-causal-inference-bb5d3da4f8ca?source=post_page-----0444b8cc65bd--------------------------------) [## 因果推断的简易方法

### 使用你最喜欢的模型与元学习器相结合，做出有效的因果声明

towardsdatascience.com](/easy-methods-for-causal-inference-bb5d3da4f8ca?source=post_page-----0444b8cc65bd--------------------------------)

在继续之前，我建议先阅读这篇文章。我向你展示了如何在你的特征形成**足够的调整集**时轻松得出因果声明，本文其余部分也将假设这一点。

该估计使用了所谓的**元学习器**。其中有 S-学习者和 T-学习者，每种都有各自的缺点。在本文中，我将向你展示另一种方法，可以视为这两种元学习器之间的权衡，从而获得更好的结果。
