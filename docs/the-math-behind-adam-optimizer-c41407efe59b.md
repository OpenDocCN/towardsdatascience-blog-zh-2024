# Adam 优化器背后的数学

> 原文：[`towardsdatascience.com/the-math-behind-adam-optimizer-c41407efe59b?source=collection_archive---------0-----------------------#2024-01-30`](https://towardsdatascience.com/the-math-behind-adam-optimizer-c41407efe59b?source=collection_archive---------0-----------------------#2024-01-30)

## 为什么 Adam 是深度学习中最受欢迎的优化器？让我们通过深入了解其数学原理并重现算法来理解它。

[](https://medium.com/@cristianleo120?source=post_page---byline--c41407efe59b--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--c41407efe59b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c41407efe59b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c41407efe59b--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--c41407efe59b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c41407efe59b--------------------------------) ·16 分钟阅读·2024 年 1 月 30 日

--

![](img/70d06b2ec8e8cb2a346295529f913efd.png)

图片由 DALLE-2 生成

如果你点击了这篇文章，你可能已经听说过 Adam，这个名字在许多 Kaggle 比赛中获得了显著的认可。通常，我们会尝试一些优化器，如 SGD、Adagrad、Adam 或 AdamW，但真正理解它们的机制则是另一回事。通过阅读本文，你将成为少数几位不仅了解 Adam 优化的原理，还能有效利用其强大功能的人之一。

顺便提一下，在我之前的文章中，我介绍了随机梯度下降的数学，如果你错过了，推荐你阅读一下。

[](https://medium.com/@cristianleo120/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79?source=post_page-----c41407efe59b--------------------------------) [## 随机梯度下降：数学与 Python 代码

### 深入了解随机梯度下降：算法、假设、优点、公式及实际应用。

medium.com](https://medium.com/@cristianleo120/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79?source=post_page-----c41407efe59b--------------------------------)

**索引**

**·** **1: 理解基础知识**

∘ 1.1: 什么是 Adam 优化器？

∘ 1.2: Adam 的机制

**·** **2. Adam 算法解析**

∘ 2.1 Adam 背后的数学

∘ 2.2 角色…
