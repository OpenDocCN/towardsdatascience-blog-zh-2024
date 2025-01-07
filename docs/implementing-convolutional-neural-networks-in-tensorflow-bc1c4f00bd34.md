# 在TensorFlow中实现卷积神经网络

> 原文：[https://towardsdatascience.com/implementing-convolutional-neural-networks-in-tensorflow-bc1c4f00bd34?source=collection_archive---------14-----------------------#2024-08-20](https://towardsdatascience.com/implementing-convolutional-neural-networks-in-tensorflow-bc1c4f00bd34?source=collection_archive---------14-----------------------#2024-08-20)

## 构建卷积神经网络的逐步代码指南

[](https://medium.com/@shreya.rao?source=post_page---byline--bc1c4f00bd34--------------------------------)[![Shreya Rao](../Images/03f13be6f5f67783d32f0798f09a4f86.png)](https://medium.com/@shreya.rao?source=post_page---byline--bc1c4f00bd34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bc1c4f00bd34--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bc1c4f00bd34--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--bc1c4f00bd34--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bc1c4f00bd34--------------------------------) ·阅读时间6分钟·2024年8月20日

--

欢迎来到我们[**深度学习插图**](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640)系列的实用实现指南。在这个系列中，我们弥合了理论与应用之间的差距，让之前文章中探讨的神经网络概念变得生动起来。

![Shreya Rao](../Images/45d3d481fab74a720c78346bc47e95fd.png)

[Shreya Rao](https://medium.com/@shreya.rao?source=post_page-----bc1c4f00bd34--------------------------------)

## 深度学习插图

[查看列表](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640?source=post_page-----bc1c4f00bd34--------------------------------)5个故事![](../Images/9668eeb3fd221bb26c2341a0ec0bfeab.png)![](../Images/1c261ce54b80b877b7737964ba5bf3f2.png)![](../Images/10364c8fdf64c9c6fb8300ce74259d00.png)

在今天的文章中，我们将使用TensorFlow构建一个卷积神经网络（CNN）。确保先阅读之前的[CNN文章](https://medium.com/r?url=https%3A%2F%2Ftowardsdatascience.com%2Fdeep-learning-illustrated-part-3-convolutional-neural-networks-96b900b0b9e0)，因为本篇文章假设你已经熟悉CNN的内部工作原理和数学基础。我们将专注于实现部分，所以预先的知识将帮助你更容易地跟随。

[](/deep-learning-illustrated-part-3-convolutional-neural-networks-96b900b0b9e0?source=post_page-----bc1c4f00bd34--------------------------------) [## 深度学习插图，第3部分：卷积神经网络

### 一本关于CNN内部工作原理的插图直观指南

[towardsdatascience.com](/deep-learning-illustrated-part-3-convolutional-neural-networks-96b900b0b9e0?source=post_page-----bc1c4f00bd34--------------------------------)

我们将创建一个相同的简单图像分类器，预测给定的图像是否为‘X’。

![](../Images/fe39b7b87a37b206006b26bd67b033a3.png)
