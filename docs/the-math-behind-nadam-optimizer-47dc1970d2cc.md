# Nadam优化器背后的数学原理

> 原文：[https://towardsdatascience.com/the-math-behind-nadam-optimizer-47dc1970d2cc?source=collection_archive---------2-----------------------#2024-05-16](https://towardsdatascience.com/the-math-behind-nadam-optimizer-47dc1970d2cc?source=collection_archive---------2-----------------------#2024-05-16)

## Nadam是深度学习中最强大的优化器之一。让我们深入探讨它的数学原理，并从零开始构建这个算法。

[](https://medium.com/@cristianleo120?source=post_page---byline--47dc1970d2cc--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--47dc1970d2cc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47dc1970d2cc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--47dc1970d2cc--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--47dc1970d2cc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47dc1970d2cc--------------------------------) ·18分钟阅读·2024年5月16日

--

![](../Images/65e6eaad6e0391dc9ba46b0a4e1451f6.png)

图片由DALL-E生成

在我们之前关于Adam优化器的讨论中，我们探讨了Adam如何通过巧妙地处理自适应学习率，改变了机器学习中的优化格局。Adam因其在各类机器学习竞赛中的成功，尤其是在Kaggle等平台上的表现，成为了优化技术中的一大标杆。然而，优化算法的进化并没有止步于此。Nadam诞生了——即Nesterov加速自适应动量估计（Nesterov-accelerated Adaptive Moment Estimation）的简称——它是Adam的先进继任者。

你无需阅读我之前关于Adam的文章就能理解本文，但如果你感兴趣，这里是链接：

[](/the-math-behind-adam-optimizer-c41407efe59b?source=post_page-----47dc1970d2cc--------------------------------) [## Adam优化器背后的数学原理

### 为什么Adam是深度学习中最流行的优化器？让我们通过深入研究其数学原理，重新创造它……

towardsdatascience.com](/the-math-behind-adam-optimizer-c41407efe59b?source=post_page-----47dc1970d2cc--------------------------------)

Nadam通过引入Nesterov动量，增强了Adam优化器，使得梯度更新具有前瞻性。这一调整不仅加速了收敛过程，还提高了朝向最优解的步伐准确性……
