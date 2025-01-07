# 卷积神经网络背后的数学

> 原文：[https://towardsdatascience.com/the-math-behind-convolutional-neural-networks-6aed775df076?source=collection_archive---------0-----------------------#2024-04-09](https://towardsdatascience.com/the-math-behind-convolutional-neural-networks-6aed775df076?source=collection_archive---------0-----------------------#2024-04-09)

## 深入了解CNN（计算机视觉的核心），理解其数学原理，从零实现，并探索其应用

[](https://medium.com/@cristianleo120?source=post_page---byline--6aed775df076--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--6aed775df076--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6aed775df076--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6aed775df076--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--6aed775df076--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6aed775df076--------------------------------) ·26分钟阅读·2024年4月9日

--

![](../Images/c23e5e95972a6884a280df1b1db93038.png)

图片来源：DALL-E

**索引**

**·** [**1: 介绍**](#637a)

· [**2: CNN架构背后的数学**](#6cca)

∘ [2.1: 卷积层](#e2f8)

∘ [2.2: 步幅](#05d6)

∘ [2.3: 填充](#e60d)

∘ [2.4: 多重滤波器与深度](#2415)

∘ [2.5: 权重共享](#cb13)

∘ [2.6: 特征图创建](#848c)

∘ [2.7: 池化层](#0829)

∘ [2.8: 全连接层](#92f5)

**·** [**3: 构建CNN的逐步指南**](#a0b0)

∘ [3.1: 设置环境](#e885)

∘ [3.2: 准备数据](#5019)

∘ [3.3: 设计CNN模型](#c0a9)

∘ [3.4: 编译模型](#4a40)

∘ [3.5: 训练CNN](#c208)

**·** [**4: 改进模型性能**](#25f7)

∘ [4.1: 数据增强](#37ff)

∘ [4.2: Dropout](#3af8)

∘ [4.3: 批量归一化](#132f)

∘ [4.4: 迁移学习](#dc75)
