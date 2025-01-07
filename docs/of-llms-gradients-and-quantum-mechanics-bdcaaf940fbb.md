# 关于LLM、梯度和量子力学

> 原文：[https://towardsdatascience.com/of-llms-gradients-and-quantum-mechanics-bdcaaf940fbb?source=collection_archive---------6-----------------------#2024-11-12](https://towardsdatascience.com/of-llms-gradients-and-quantum-mechanics-bdcaaf940fbb?source=collection_archive---------6-----------------------#2024-11-12)

## 量子计算是否能帮助我们提高训练大型神经网络语言模型（LLM）的能力？

[](https://riccardo-disipio.medium.com/?source=post_page---byline--bdcaaf940fbb--------------------------------)[![Riccardo Di Sipio](../Images/07d5e8829a0bba4f32a91e261378d969.png)](https://riccardo-disipio.medium.com/?source=post_page---byline--bdcaaf940fbb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdcaaf940fbb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bdcaaf940fbb--------------------------------) [Riccardo Di Sipio](https://riccardo-disipio.medium.com/?source=post_page---byline--bdcaaf940fbb--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdcaaf940fbb--------------------------------) ·13分钟阅读·2024年11月12日

--

![](../Images/c29f7df67db71e4b0399ae2ff25a9683.png)

图片来源：Alessio Soggetti (@asoggetti) 来自 Unsplash.com

## 什么是“训练”？

在人工智能（AI）研究的术语中，“训练”是指优化一个统计*模型*，通常表现为一个[神经网络](https://en.wikipedia.org/wiki/Neural_network_(machine_learning))，使其能够根据输入数据和这些预测的好坏（“成本”或“损失”函数）来做出预测。这样的过程可以通过三种[主要范式](https://www.geeksforgeeks.org/supervised-unsupervised-learning/)来进行：*监督学习*、*无监督学习*（通常是[自回归](https://aws.amazon.com/what-is/autoregressive-models/)的），以及*强化学习*。在**监督学习**中，每个数据点都有标签，因此可以将模型的预测结果与真实值进行直接比较（*例如*：这是猫还是狗的图像）。在**无监督学习**中，没有明确的标签，但通过从数据本身提取的特征进行比较（*例如*：预测句子中的下一个单词）。最后，**强化学习**是基于通过统计模型与环境的互动来优化一系列决策（预测）的长期回报（例如：在黄色交通灯前，汽车应该减速还是加速？）。

在所有这些情况下，**模型参数的优化**是一个漫长的过程，需要…
