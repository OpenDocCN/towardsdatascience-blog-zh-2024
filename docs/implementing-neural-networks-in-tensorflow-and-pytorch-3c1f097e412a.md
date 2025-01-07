# 在TensorFlow（以及PyTorch）中实现神经网络

> 原文：[https://towardsdatascience.com/implementing-neural-networks-in-tensorflow-and-pytorch-3c1f097e412a?source=collection_archive---------2-----------------------#2024-07-08](https://towardsdatascience.com/implementing-neural-networks-in-tensorflow-and-pytorch-3c1f097e412a?source=collection_archive---------2-----------------------#2024-07-08)

## 构建神经网络的逐步代码指南

[](https://medium.com/@shreya.rao?source=post_page---byline--3c1f097e412a--------------------------------)[![Shreya Rao](../Images/03f13be6f5f67783d32f0798f09a4f86.png)](https://medium.com/@shreya.rao?source=post_page---byline--3c1f097e412a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3c1f097e412a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3c1f097e412a--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--3c1f097e412a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3c1f097e412a--------------------------------) ·阅读时间：6分钟·2024年7月8日

--

欢迎来到我们[**深度学习图解**](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640)系列的实践实施指南。在本系列中，我们将弥合理论与应用之间的差距，将之前文章中探讨的神经网络概念生动呈现出来。

![Shreya Rao](../Images/45d3d481fab74a720c78346bc47e95fd.png)

[Shreya Rao](https://medium.com/@shreya.rao?source=post_page-----3c1f097e412a--------------------------------)

## 深度学习图解

[查看列表](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640?source=post_page-----3c1f097e412a--------------------------------)5个故事！[](../Images/9668eeb3fd221bb26c2341a0ec0bfeab.png)![](../Images/1c261ce54b80b877b7737964ba5bf3f2.png)![](../Images/10364c8fdf64c9c6fb8300ce74259d00.png)

记得我们讨论过的[简单神经网络](https://medium.com/towards-data-science/deep-learning-illustrated-part-2-how-does-a-neural-network-learn-481f70c1b474)用于预测冰淇淋收入吗？我们将使用TensorFlow这一强大的工具来构建它，TensorFlow是一个用于创建神经网络的工具。

[](/deep-learning-illustrated-part-2-how-does-a-neural-network-learn-481f70c1b474?source=post_page-----3c1f097e412a--------------------------------) [## 深度学习图解，第2部分：神经网络是如何学习的？

### 一本插图丰富、直观的神经网络指南

towardsdatascience.com](/deep-learning-illustrated-part-2-how-does-a-neural-network-learn-481f70c1b474?source=post_page-----3c1f097e412a--------------------------------)

重点是：我们将在不到5分钟内，使用仅27行代码实现这一过程！

**首先让我们从：什么是** [**TensorFlow**](https://www.tensorflow.org/)**？**

TensorFlow 是一个全面的工具、库和社区资源生态系统，用于构建和部署机器学习应用。由谷歌开发，它旨在具有灵活性和高效性，能够在从 CPU 到 GPU 甚至专用硬件的各种平台上运行……
