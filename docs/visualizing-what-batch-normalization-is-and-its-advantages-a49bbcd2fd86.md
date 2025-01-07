# 可视化批量归一化及其优点

> 原文：[https://towardsdatascience.com/visualizing-what-batch-normalization-is-and-its-advantages-a49bbcd2fd86?source=collection_archive---------8-----------------------#2024-02-03](https://towardsdatascience.com/visualizing-what-batch-normalization-is-and-its-advantages-a49bbcd2fd86?source=collection_archive---------8-----------------------#2024-02-03)

## 优化神经网络训练中的批量归一化

[](https://qtalen.medium.com/?source=post_page---byline--a49bbcd2fd86--------------------------------)[![Peng Qian](../Images/9ce9aeb381ec6b017c1ee5d4714937e2.png)](https://qtalen.medium.com/?source=post_page---byline--a49bbcd2fd86--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a49bbcd2fd86--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a49bbcd2fd86--------------------------------) [Peng Qian](https://qtalen.medium.com/?source=post_page---byline--a49bbcd2fd86--------------------------------)  

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a49bbcd2fd86--------------------------------) ·阅读时间 5 分钟·2024年2月3日

--

![](../Images/8cc4a86665dbb2044eadc71bc16939b9.png)

可视化批量归一化及其优点。图片来源：作者

# 介绍

当你在进行深度学习项目时，是否遇到过这样的情况：神经网络层数越多，训练速度越慢？

如果你的回答是“是的”，那么恭喜你，是时候考虑使用批量归一化了。

# 什么是批量归一化？

顾名思义，批量归一化是一种技术，批量训练数据在当前层激活后、进入下一层之前会进行标准化。其工作原理如下：

1.  *整个数据集被随机划分为 N 个批次，且不重复，每个批次具有一个 mini_batch 大小，用于训练。*

1.  *对于第 i 个批次，使用公式标准化批次内的数据分布：* `*(Xi - Xmean) / Xstd*`*。*

1.  *对标准化后的数据进行缩放和平移，使用公式* `*γXi + β*` *，以便在需要时让神经网络撤销标准化的效果。*

步骤看起来很简单，是吗？那么，批量归一化有哪些优点呢？

# 批量归一化的优点
