# 为什么批量归一化对深度学习至关重要

> 原文：[https://towardsdatascience.com/why-batch-normalization-matters-for-deep-learning-3e5f4d71f567?source=collection_archive---------7-----------------------#2024-11-25](https://towardsdatascience.com/why-batch-normalization-matters-for-deep-learning-3e5f4d71f567?source=collection_archive---------7-----------------------#2024-11-25)

## 探索批量归一化在简化神经网络训练和提高模型性能中的作用

[](https://medium.com/@niklas_lang?source=post_page---byline--3e5f4d71f567--------------------------------)[![Niklas Lang](../Images/5fa71386db00d248438c588c5ae79c67.png)](https://medium.com/@niklas_lang?source=post_page---byline--3e5f4d71f567--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3e5f4d71f567--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3e5f4d71f567--------------------------------) [Niklas Lang](https://medium.com/@niklas_lang?source=post_page---byline--3e5f4d71f567--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3e5f4d71f567--------------------------------) ·阅读时间11分钟·2024年11月25日

--

![](../Images/841e3523b94cac1fefe7a3667b750797.png)

照片来自[Petra Reid](https://unsplash.com/@createinme_nz?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的作品

批量归一化（Batch normalization）近年来已成为训练神经网络的重要技术。它使训练过程更加高效和稳定，这是一个关键因素，特别是在处理大型和深度网络时。最初它是为了解决内部协方差偏移问题而提出的。

本文将探讨训练神经网络过程中所涉及的问题，以及批量归一化如何解决这些问题。我们将详细描述其过程，并展示如何在Python中实现批量归一化并将其集成到现有模型中。我们还将考虑该方法的优缺点，以判断是否值得使用。

# 训练深度神经网络时会出现哪些问题？

在训练[深度神经网络](https://databasecamp.de/en/ml/artificial-neural-networks)时，[反向传播](https://databasecamp.de/en/ml/backpropagation-basics)会在每次运行后发生。预测误差从网络的后层逐层传递。在此过程中，个别神经元的权重会被调整，以便尽快减少误差。这一过程假设其他层保持不变，并且……
