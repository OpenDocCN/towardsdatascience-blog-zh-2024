# 如何实现最先进的掩码自编码器（MAE）

> 原文：[https://towardsdatascience.com/how-to-implement-state-of-the-art-masked-autoencoders-mae-6f454b736087?source=collection_archive---------9-----------------------#2024-09-16](https://towardsdatascience.com/how-to-implement-state-of-the-art-masked-autoencoders-mae-6f454b736087?source=collection_archive---------9-----------------------#2024-09-16)

## **使用视觉变换器（Vision Transformers）构建 MAE 的一步步指南**

[](https://medium.com/@francoisporcher?source=post_page---byline--6f454b736087--------------------------------)[![François Porcher](../Images/9ddb233f8cadbd69026bd79e2bd62dea.png)](https://medium.com/@francoisporcher?source=post_page---byline--6f454b736087--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6f454b736087--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6f454b736087--------------------------------) [François Porcher](https://medium.com/@francoisporcher?source=post_page---byline--6f454b736087--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6f454b736087--------------------------------) ·阅读时间：7分钟·2024年9月16日

--

大家好！对于还不认识我的朋友们，我叫 Francois，我是 Meta 的研究科学家。我热衷于解释先进的 AI 概念，并使其更加易于理解。

今天，我很高兴深入探讨计算机视觉领域，继视觉变换器（Vision Transformers）之后最重要的突破之一：**掩码自编码器（MAE）**。本文作为我之前文章的实践实现伴随教程：[掩码自编码器（MAE）终极指南](https://medium.com/ai-advances/the-ultimate-guide-to-masked-autoencoders-mae-87aa6883ff47)

在以下教程中，我们将使用此 GitHub 仓库中的代码：

[](https://github.com/FrancoisPorcher/awesome-ai-tutorials?source=post_page-----6f454b736087--------------------------------) [## GitHub - FrancoisPorcher/awesome-ai-tutorials: 最佳 AI 教程合集，帮助你成为数据科学的高手…

### 最佳的 AI 教程合集，帮助你成为数据科学的高手！ - FrancoisPorcher/awesome-ai-tutorials

github.com](https://github.com/FrancoisPorcher/awesome-ai-tutorials?source=post_page-----6f454b736087--------------------------------)

这里简要提醒一下其工作原理：

![](../Images/4bb371ea36f5a017424c55d6032f01dd.png)

图像来自文章 [MAE 是可扩展的学习器](https://arxiv.org/abs/2111.06377)

**以下是该方法的工作原理：**

1\. 图像被分割成多个补丁。

2\. 这些补丁的一个子集被随机掩盖。

3\. 仅将可见的补丁输入到编码器中 **（这至关重要）**。
