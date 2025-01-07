# AdEMAMix：深入探讨一种新的深度神经网络优化器

> 原文：[https://towardsdatascience.com/ademamix-a-deep-dive-into-a-new-optimizer-for-your-deep-neural-network-6168e2c1da35?source=collection_archive---------10-----------------------#2024-09-19](https://towardsdatascience.com/ademamix-a-deep-dive-into-a-new-optimizer-for-your-deep-neural-network-6168e2c1da35?source=collection_archive---------10-----------------------#2024-09-19)

## 一种比ADAM优化器更好且更快速的选择，来自苹果研究团队

[](https://saptashwa.medium.com/?source=post_page---byline--6168e2c1da35--------------------------------)[![Saptashwa Bhattacharyya](../Images/b01238113a1f6b91cb6fb0fbfa50303a.png)](https://saptashwa.medium.com/?source=post_page---byline--6168e2c1da35--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6168e2c1da35--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6168e2c1da35--------------------------------) [Saptashwa Bhattacharyya](https://saptashwa.medium.com/?source=post_page---byline--6168e2c1da35--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6168e2c1da35--------------------------------) ·12分钟阅读·2024年9月19日

--

![](../Images/48723d07a415766eeb3f87f164e02730.png)

我们该走哪条路？让我们优化一下吧（由DALL·E 3根据作者提示生成）

深度神经网络（DNN）被认为是通过训练在大规模数据集中发现模式的最有效工具之一。在训练问题的核心，我们面临复杂的损失函数地形，DNN的训练归结为随着迭代次数的增加优化损失。几种常用的优化器包括随机梯度下降、RMSProp（均方根传播）、Adam（自适应矩估计）等。

最近（2024年9月），来自苹果（和瑞士联邦理工学院）的研究人员提出了一种新的优化器AdEMAMix¹，研究表明它在语言建模和图像分类任务中，比AdamW优化器表现得更好、更快。

在这篇文章中，我将详细介绍这一优化器背后的数学概念，并讨论本文中提出的一些非常有趣的结果。本文将涵盖的主题包括：

+   Adam优化器回顾

+   Adam中的指数加权移动平均（EMA）。

+   AdEMAMix背后的主要思想：两种EMA的混合。

+   AdEMAMix中的指数衰减率调度器。
