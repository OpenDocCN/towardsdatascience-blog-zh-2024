# SAM：Segment Anything Model

> 原文：[`towardsdatascience.com/sam-segment-anything-model-4b25a47245f2?source=collection_archive---------4-----------------------#2024-01-22`](https://towardsdatascience.com/sam-segment-anything-model-4b25a47245f2?source=collection_archive---------4-----------------------#2024-01-22)

## 快速通过 SAM 定制您的产品落地页

[](https://medium.com/@rjguedes?source=post_page---byline--4b25a47245f2--------------------------------)![Rafael Guedes](https://medium.com/@rjguedes?source=post_page---byline--4b25a47245f2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4b25a47245f2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4b25a47245f2--------------------------------) [Rafael Guedes](https://medium.com/@rjguedes?source=post_page---byline--4b25a47245f2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4b25a47245f2--------------------------------) ·10 分钟阅读·2024 年 1 月 22 日

--

# 介绍

Transformers 模型已经广泛应用于自然语言处理的场景，但它们也可以应用于人工智能的其他多个领域，例如时间序列预测或计算机视觉。

一些将 Transformers 模型应用于计算机视觉的出色例子包括：用于图像生成的 Stable Diffusion，用于目标检测的 Detection Transformer，或者更近期的，用于图像分割的 SAM。这些模型带来的巨大好处是，我们可以使用文本提示来操控图像，而无需太多努力，只要有一个好的提示。

这类模型的应用场景是无穷无尽的，特别是如果你在一家电子商务公司工作的话。一种简单、耗时且昂贵的应用场景是从拍摄物品到将其发布到网站销售的过程。公司需要拍摄物品，去除使用的道具，并且在发布物品之前，最终需要对道具留下的空洞进行填充。如果整个过程可以由 AI 自动化，我们的人力资源只需处理复杂的用例并审核 AI 完成的任务，那该有多好？

在这篇文章中，我将详细解释 SAM，一个图像分割模型，并展示其在一个假设用例中的实现，假设我们想进行 A/B 测试，以了解哪种背景类型能提高转化率。
