# 扩散损失：每一步的解释

> 原文：[https://towardsdatascience.com/diffusion-loss-every-step-explained-8c19c5e1349b?source=collection_archive---------5-----------------------#2024-07-31](https://towardsdatascience.com/diffusion-loss-every-step-explained-8c19c5e1349b?source=collection_archive---------5-----------------------#2024-07-31)

## 论文回顾：去噪扩散概率模型（DDPM）

[](https://saptashwa.medium.com/?source=post_page---byline--8c19c5e1349b--------------------------------)[![Saptashwa Bhattacharyya](../Images/b01238113a1f6b91cb6fb0fbfa50303a.png)](https://saptashwa.medium.com/?source=post_page---byline--8c19c5e1349b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8c19c5e1349b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8c19c5e1349b--------------------------------) [Saptashwa Bhattacharyya](https://saptashwa.medium.com/?source=post_page---byline--8c19c5e1349b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8c19c5e1349b--------------------------------) ·15分钟阅读·2024年7月31日

--

![](../Images/d07d4e4d4f3eb0992d5884f44b5501e1.png)

使用DALLE-3生成（作者提供）

在我们处于图像生成的激动人心的时代，本文将重点介绍稳定扩散模型背后的数学原理和隐含直觉。

本文旨在解读损失函数如何简化为图像中真实噪声和预测噪声之间的简单平方差项，正如《去噪扩散概率模型》论文¹所示。

如果你想回顾在变分贝叶斯方法中证据下界（ELBO）的概念和步骤，请查看之前关于[潜在变量模型](/latent-variables-expectation-maximization-algorithm-fb15c4e0f32c)和[概率主成分分析](https://towardsdatascience.com/probabilistic-view-of-principal-component-analysis-9c1bbb3f167)的详细文章。

不再耽搁，让我们开始吧！

## 符号与定义：

让我们从一些将多次使用的符号开始。

*x_0*：这表示在时间步长0时的图像，即原始图像，处于过程的起始阶段。有时也指在去噪过程的最后一步恢复的图像。

*x_T*：这是最终时间步长的图像。此时，图像仅仅是各向同性的高斯噪声。
