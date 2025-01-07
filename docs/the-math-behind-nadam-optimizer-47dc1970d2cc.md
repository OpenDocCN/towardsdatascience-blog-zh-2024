# Nadam 优化器背后的数学原理

> 原文：[`towardsdatascience.com/the-math-behind-nadam-optimizer-47dc1970d2cc?source=collection_archive---------2-----------------------#2024-05-16`](https://towardsdatascience.com/the-math-behind-nadam-optimizer-47dc1970d2cc?source=collection_archive---------2-----------------------#2024-05-16)

## Nadam 是深度学习中最强大的优化器之一。让我们深入探讨它的数学原理，并从零开始构建这个算法。

[](https://medium.com/@cristianleo120?source=post_page---byline--47dc1970d2cc--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--47dc1970d2cc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47dc1970d2cc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47dc1970d2cc--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--47dc1970d2cc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47dc1970d2cc--------------------------------) ·18 分钟阅读·2024 年 5 月 16 日

--

![](img/65e6eaad6e0391dc9ba46b0a4e1451f6.png)

图片由 DALL-E 生成

在我们之前关于 Adam 优化器的讨论中，我们探讨了 Adam 如何通过巧妙地处理自适应学习率，改变了机器学习中的优化格局。Adam 因其在各类机器学习竞赛中的成功，尤其是在 Kaggle 等平台上的表现，成为了优化技术中的一大标杆。然而，优化算法的进化并没有止步于此。Nadam 诞生了——即 Nesterov 加速自适应动量估计（Nesterov-accelerated Adaptive Moment Estimation）的简称——它是 Adam 的先进继任者。

你无需阅读我之前关于 Adam 的文章就能理解本文，但如果你感兴趣，这里是链接：

[](/the-math-behind-adam-optimizer-c41407efe59b?source=post_page-----47dc1970d2cc--------------------------------) ## Adam 优化器背后的数学原理

### 为什么 Adam 是深度学习中最流行的优化器？让我们通过深入研究其数学原理，重新创造它……

towardsdatascience.com

Nadam 通过引入 Nesterov 动量，增强了 Adam 优化器，使得梯度更新具有前瞻性。这一调整不仅加速了收敛过程，还提高了朝向最优解的步伐准确性……
