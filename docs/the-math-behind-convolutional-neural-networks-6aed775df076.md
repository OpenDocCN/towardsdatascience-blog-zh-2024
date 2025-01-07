# 卷积神经网络背后的数学

> 原文：[`towardsdatascience.com/the-math-behind-convolutional-neural-networks-6aed775df076?source=collection_archive---------0-----------------------#2024-04-09`](https://towardsdatascience.com/the-math-behind-convolutional-neural-networks-6aed775df076?source=collection_archive---------0-----------------------#2024-04-09)

## 深入了解 CNN（计算机视觉的核心），理解其数学原理，从零实现，并探索其应用

[](https://medium.com/@cristianleo120?source=post_page---byline--6aed775df076--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--6aed775df076--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6aed775df076--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6aed775df076--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--6aed775df076--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6aed775df076--------------------------------) ·26 分钟阅读·2024 年 4 月 9 日

--

![](img/c23e5e95972a6884a280df1b1db93038.png)

图片来源：DALL-E

**索引**

**·** **1: 介绍**

· **2: CNN 架构背后的数学**

∘ 2.1: 卷积层

∘ 2.2: 步幅

∘ 2.3: 填充

∘ 2.4: 多重滤波器与深度

∘ 2.5: 权重共享

∘ 2.6: 特征图创建

∘ 2.7: 池化层

∘ 2.8: 全连接层

**·** **3: 构建 CNN 的逐步指南**

∘ 3.1: 设置环境

∘ 3.2: 准备数据

∘ 3.3: 设计 CNN 模型

∘ 3.4: 编译模型

∘ 3.5: 训练 CNN

**·** **4: 改进模型性能**

∘ 4.1: 数据增强

∘ 4.2: Dropout

∘ 4.3: 批量归一化

∘ 4.4: 迁移学习
