# Kolmogorov-Arnold Networks：神经网络领域的最新进展，简明解释

> 原文：[`towardsdatascience.com/kolmogorov-arnold-networks-the-latest-advance-in-neural-networks-simply-explained-f083cf994a85?source=collection_archive---------0-----------------------#2024-05-12`](https://towardsdatascience.com/kolmogorov-arnold-networks-the-latest-advance-in-neural-networks-simply-explained-f083cf994a85?source=collection_archive---------0-----------------------#2024-05-12)

## 这种新型网络正在机器学习领域掀起波澜。

[](https://medium.com/@theo.wolf?source=post_page---byline--f083cf994a85--------------------------------)![Theo Wolf](https://medium.com/@theo.wolf?source=post_page---byline--f083cf994a85--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f083cf994a85--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f083cf994a85--------------------------------) [Theo Wolf](https://medium.com/@theo.wolf?source=post_page---byline--f083cf994a85--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f083cf994a85--------------------------------) ·9 分钟阅读·2024 年 5 月 12 日

--

![](img/e16cf3f974c2c32b49abf83ee0870196.png)

MLP 与 KAN 的比较。图片来自[论文](https://arxiv.org/abs/2404.19756)。

今年四月，一篇名为：[KAN: Kolmogorov–Arnold Networks](https://arxiv.org/abs/2404.19756)的论文出现在 arXiv 上。宣布这篇论文的推文获得了大约 5 千个点赞，对于论文的宣布来说，这已经算是相当火爆了。附带的[GitHub 仓库](https://github.com/KindXiaoming/pykan)已经有 7.6k 个星标，并且还在不断增加。

Kolmogorov-Arnold 网络（KAN）是一种全新的神经网络构建模块。它的目标是比多层感知器（MLP）更具表现力、更不容易过拟合，并且更具可解释性。MLP 在深度学习模型中无处不在。例如，我们知道它们被用在像 GPT-2、3 和（可能）4 等模型的 Transformer 块之间。对 MLP 的改进将对机器学习领域产生广泛的影响。

# MLP 简述

MLP 实际上是一种非常古老的架构，起源于 50 年代。其思想是复制大脑的结构；有很多……
