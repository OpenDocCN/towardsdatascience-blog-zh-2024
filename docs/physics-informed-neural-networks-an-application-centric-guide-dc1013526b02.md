# 物理启发的神经网络：面向应用的指南

> 原文：[https://towardsdatascience.com/physics-informed-neural-networks-an-application-centric-guide-dc1013526b02?source=collection_archive---------2-----------------------#2024-02-09](https://towardsdatascience.com/physics-informed-neural-networks-an-application-centric-guide-dc1013526b02?source=collection_archive---------2-----------------------#2024-02-09)

## PINN在现实世界中的成功案例的全面概述

[](https://shuaiguo.medium.com/?source=post_page---byline--dc1013526b02--------------------------------)[![Shuai Guo](../Images/d673c066f8006079be5bf92757e73a59.png)](https://shuaiguo.medium.com/?source=post_page---byline--dc1013526b02--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc1013526b02--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dc1013526b02--------------------------------) [Shuai Guo](https://shuaiguo.medium.com/?source=post_page---byline--dc1013526b02--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc1013526b02--------------------------------) ·阅读时长36分钟·2024年2月9日

--

![](../Images/52e90c39a4c58c955195c07f92d1d776.png)

图片由DALL-E生成。

当谈到将机器学习应用于物理系统建模时，越来越多的实践者开始远离纯粹的数据驱动策略，转而接受一种混合思维方式，在这种方式下，丰富的先验物理知识（例如，控制微分方程）与数据一起用于增强模型训练。

在这种背景下，***物理启发的神经网络***（PINNs）作为一个多功能概念应运而生，并在有效解决现实世界问题方面取得了许多成功案例。

作为一个渴望采用PINNs的实践者，我非常希望了解最新的训练算法进展，以及PINNs在现实应用中的**新颖用例**。然而，我常见的一个痛点是，尽管有大量的研究论文/博客总结了有效的PINN算法，但关于PINNs新颖用例的概述却很难找到。一个明显的原因是，与领域无关的训练算法不同，PINN用例的报告分散在各个工程领域，并且对于通常专注于某一特定领域的实践者来说，难以直接访问。因此，我经常发现自己…
