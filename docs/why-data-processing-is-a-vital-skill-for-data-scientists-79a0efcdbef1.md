# 为什么数据处理对数据科学家至关重要

> 原文：[https://towardsdatascience.com/why-data-processing-is-a-vital-skill-for-data-scientists-79a0efcdbef1?source=collection_archive---------11-----------------------#2024-07-31](https://towardsdatascience.com/why-data-processing-is-a-vital-skill-for-data-scientists-79a0efcdbef1?source=collection_archive---------11-----------------------#2024-07-31)

## 观点

## 随着预训练AI模型和大型语言模型（LLM）数据分析的兴起，独特的数据是唯一能让你与众不同的东西。

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--79a0efcdbef1--------------------------------)[![Caroline Arnold](../Images/fb13ba36e302d8161b67c4888d0601e4.png)](https://medium.com/@caroline.arnold_63207?source=post_page---byline--79a0efcdbef1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--79a0efcdbef1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--79a0efcdbef1--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--79a0efcdbef1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--79a0efcdbef1--------------------------------)·阅读时间：4分钟·2024年7月31日

--

![](../Images/cd14c076c137eddb173537964d89d486.png)

图片由作者使用[Midjourney](https://www.midjourney.com/jobs/cdacac3e-6ae2-43c1-a771-3f1905f0bffd?index=2)创作。

当我在完成物理学博士学位后转行从事数据科学工作时，我被复杂的模型和有洞察力的数据分析所吸引。

四年后，我知道这些工作的方面逐渐变得不那么重要了。

成功的AI应用程序的首要成分是数据。而在我的任何项目中，最大时间消耗就是数据处理。

## 欢迎来到现实世界

入门课程侧重于模型开发和理解训练神经网络的内部工作原理。

我们学习编写自己的训练循环，选择合适的验证指标，并理解偏差-方差权衡。

学生们使用像[MNIST](https://pytorch.org/vision/main/generated/torchvision.datasets.MNIST.html)这样的现成数据集。我们的课程对数据部分只是略微涉及，实际上对于教程来说，数据处理就像是输入

```py
from torchvision.datasets import MNIST
dataset = MNIST('./data', download=True)
```
