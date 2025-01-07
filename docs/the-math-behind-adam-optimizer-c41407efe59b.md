# Adam优化器背后的数学

> 原文：[https://towardsdatascience.com/the-math-behind-adam-optimizer-c41407efe59b?source=collection_archive---------0-----------------------#2024-01-30](https://towardsdatascience.com/the-math-behind-adam-optimizer-c41407efe59b?source=collection_archive---------0-----------------------#2024-01-30)

## 为什么Adam是深度学习中最受欢迎的优化器？让我们通过深入了解其数学原理并重现算法来理解它。

[](https://medium.com/@cristianleo120?source=post_page---byline--c41407efe59b--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--c41407efe59b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c41407efe59b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c41407efe59b--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--c41407efe59b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c41407efe59b--------------------------------) ·16分钟阅读·2024年1月30日

--

![](../Images/70d06b2ec8e8cb2a346295529f913efd.png)

图片由DALLE-2生成

如果你点击了这篇文章，你可能已经听说过Adam，这个名字在许多Kaggle比赛中获得了显著的认可。通常，我们会尝试一些优化器，如SGD、Adagrad、Adam或AdamW，但真正理解它们的机制则是另一回事。通过阅读本文，你将成为少数几位不仅了解Adam优化的原理，还能有效利用其强大功能的人之一。

顺便提一下，在我之前的文章中，我介绍了随机梯度下降的数学，如果你错过了，推荐你阅读一下。

[](https://medium.com/@cristianleo120/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79?source=post_page-----c41407efe59b--------------------------------) [## 随机梯度下降：数学与Python代码

### 深入了解随机梯度下降：算法、假设、优点、公式及实际应用。

medium.com](https://medium.com/@cristianleo120/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79?source=post_page-----c41407efe59b--------------------------------)

**索引**

**·** [**1: 理解基础知识**](#e382)

∘ [1.1: 什么是Adam优化器？](#63c0)

∘ [1.2: Adam的机制](#de8c)

**·** [**2. Adam算法解析**](#bc0c)

∘ [2.1 Adam背后的数学](#5ff9)

∘ [2.2 角色](#3663)…
