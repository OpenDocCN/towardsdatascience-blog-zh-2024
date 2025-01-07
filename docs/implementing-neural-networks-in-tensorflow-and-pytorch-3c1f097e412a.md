# 在 TensorFlow（以及 PyTorch）中实现神经网络

> 原文：[`towardsdatascience.com/implementing-neural-networks-in-tensorflow-and-pytorch-3c1f097e412a?source=collection_archive---------2-----------------------#2024-07-08`](https://towardsdatascience.com/implementing-neural-networks-in-tensorflow-and-pytorch-3c1f097e412a?source=collection_archive---------2-----------------------#2024-07-08)

## 构建神经网络的逐步代码指南

[](https://medium.com/@shreya.rao?source=post_page---byline--3c1f097e412a--------------------------------)![Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--3c1f097e412a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3c1f097e412a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3c1f097e412a--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--3c1f097e412a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3c1f097e412a--------------------------------) ·阅读时间：6 分钟·2024 年 7 月 8 日

--

欢迎来到我们[**深度学习图解**](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640)系列的实践实施指南。在本系列中，我们将弥合理论与应用之间的差距，将之前文章中探讨的神经网络概念生动呈现出来。

![Shreya Rao](img/45d3d481fab74a720c78346bc47e95fd.png)

[Shreya Rao](https://medium.com/@shreya.rao?source=post_page-----3c1f097e412a--------------------------------)

## 深度学习图解

[查看列表](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640?source=post_page-----3c1f097e412a--------------------------------)5 个故事！[](../Images/9668eeb3fd221bb26c2341a0ec0bfeab.png)![](img/1c261ce54b80b877b7737964ba5bf3f2.png)![](img/10364c8fdf64c9c6fb8300ce74259d00.png)

记得我们讨论过的[简单神经网络](https://medium.com/towards-data-science/deep-learning-illustrated-part-2-how-does-a-neural-network-learn-481f70c1b474)用于预测冰淇淋收入吗？我们将使用 TensorFlow 这一强大的工具来构建它，TensorFlow 是一个用于创建神经网络的工具。

[](/deep-learning-illustrated-part-2-how-does-a-neural-network-learn-481f70c1b474?source=post_page-----3c1f097e412a--------------------------------) ## 深度学习图解，第二部分：神经网络是如何学习的？

### 一本插图丰富、直观的神经网络指南

towardsdatascience.com

重点是：我们将在不到 5 分钟内，使用仅 27 行代码实现这一过程！

**首先让我们从：什么是** [**TensorFlow**](https://www.tensorflow.org/)**？**

TensorFlow 是一个全面的工具、库和社区资源生态系统，用于构建和部署机器学习应用。由谷歌开发，它旨在具有灵活性和高效性，能够在从 CPU 到 GPU 甚至专用硬件的各种平台上运行……
