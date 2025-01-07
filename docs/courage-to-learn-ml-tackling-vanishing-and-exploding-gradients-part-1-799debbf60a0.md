# 学习机器学习的勇气：解决梯度消失和梯度爆炸问题（第一部分）

> 原文：[`towardsdatascience.com/courage-to-learn-ml-tackling-vanishing-and-exploding-gradients-part-1-799debbf60a0?source=collection_archive---------15-----------------------#2024-02-05`](https://towardsdatascience.com/courage-to-learn-ml-tackling-vanishing-and-exploding-gradients-part-1-799debbf60a0?source=collection_archive---------15-----------------------#2024-02-05)

## 消解深度神经网络梯度问题：一勺解决方案和见解

[](https://amyma101.medium.com/?source=post_page---byline--799debbf60a0--------------------------------)![Amy Ma](https://amyma101.medium.com/?source=post_page---byline--799debbf60a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--799debbf60a0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--799debbf60a0--------------------------------) [Amy Ma](https://amyma101.medium.com/?source=post_page---byline--799debbf60a0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--799debbf60a0--------------------------------) ·13 分钟阅读·2024 年 2 月 5 日

--

![](img/ea2e2eaf6631a22a9257066dd20ae32f.png)

图片由作者使用 ChatGPT 制作。

在‘[学习机器学习的勇气](https://towardsdatascience.com/tagged/courage-to-learn-ml)’系列的上一期中，我们的学习者和导师专注于学习深度神经网络训练的两个基本理论——梯度下降和反向传播。

他们的旅程从[梯度下降在最小化损失函数中的重要性](https://towardsdatascience.com/courage-to-learn-ml-a-detailed-exploration-of-gradient-descent-and-popular-optimizers-022ecf97be7d)开始。出于对在多个隐藏层中计算梯度的复杂性的好奇，学习者随后转向了[反向传播](https://towardsdatascience.com/courage-to-learn-ml-explain-backpropagation-from-mathematical-theory-to-coding-practice-21e670415378)。通过将反向传播分解为三个组成部分，学习者了解了反向传播及其如何利用链式法则高效计算这些层中的梯度。在这次问答环节中，学习者质疑，在像 PyTorch 和 TensorFlow 这样的自动化深度学习框架盛行的时代，理解这些复杂过程的重要性。

这是我们深入探讨深度学习的第一篇文章，内容由学习者与导师之间的互动引导。为了便于消化，我决定将我的深度神经网络系列分解为更易于处理的小部分。这样，我可以深入探索每个概念，而不会让你感到不堪重负。
