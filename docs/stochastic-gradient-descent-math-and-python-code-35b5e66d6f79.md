# 随机梯度下降背后的数学原理

> 原文：[https://towardsdatascience.com/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79?source=collection_archive---------0-----------------------#2024-01-16](https://towardsdatascience.com/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79?source=collection_archive---------0-----------------------#2024-01-16)

## 随机梯度下降的深度解析：算法、假设、优点、公式和实际应用

[](https://medium.com/@cristianleo120?source=post_page---byline--35b5e66d6f79--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--35b5e66d6f79--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--35b5e66d6f79--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--35b5e66d6f79--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--35b5e66d6f79--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--35b5e66d6f79--------------------------------) ·18分钟阅读·2024年1月16日

--

![](../Images/188abc5dbac543899ab3d77a9e4711ee.png)

图片来自 [DALL-E-2](https://openai.com/dall-e-2)

# 介绍

上面的图像不仅仅是吸引你阅读本文的一个视觉元素（尽管它的篇幅较长），它还代表了SGD算法在寻找全局最小值过程中的潜在旅程。在这个旅程中，它穿越崎岖的路径，其中高度代表了损失。如果现在这一点还不清楚，别担心，等到本文结束时你就能理解了。

**索引：**

· [1：理解基础概念](#6a59)

∘ [1.1：什么是梯度下降](#2d34)

∘ [1.2：随机梯度下降中的“随机”](#5b93)

**·** [**2：SGD的机制**](#d6f5)

∘ [2.1：算法解释](#7563)

∘ [2.2：理解学习率](#f1f1)

**·** [**3：SGD在实践中的应用**](#c500)

∘ [3.1：在机器学习模型中实现SGD](#c4db)

∘ [3.2：在Sci-kit Learn和Tensorflow中的SGD](#451b)

**·** [**4：优势与挑战**](#0e3d)

∘ [4.1：为什么选择SGD？](#de7c)

∘ [4.2：克服SGD中的挑战](#c053)

**·** [**5：超越基础SGD**](#5a3f)

∘ [5.1：SGD的变种](#19d7)

∘ [5.2：SGD的未来](#b6e0)

**·** [**结论**](#6db6)
