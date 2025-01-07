# 如何做：时间序列中的基准模型

> 原文：[https://towardsdatascience.com/baseline-models-in-time-series-c76d44a826b3?source=collection_archive---------6-----------------------#2024-03-07](https://towardsdatascience.com/baseline-models-in-time-series-c76d44a826b3?source=collection_archive---------6-----------------------#2024-03-07)

## 为什么（以及如何）在训练最终模型之前创建基准模型

[](https://medium.com/@pelletierhaden?source=post_page---byline--c76d44a826b3--------------------------------)[![Haden Pelletier](../Images/8f73fc8222e783883c4ebcaee14513e0.png)](https://medium.com/@pelletierhaden?source=post_page---byline--c76d44a826b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c76d44a826b3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c76d44a826b3--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--c76d44a826b3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c76d44a826b3--------------------------------) ·5分钟阅读·2024年3月7日

--

![](../Images/55166b86fc3d444ee4d57ade1d13c333.png)

图片来自 [Zetong Li](https://unsplash.com/@zetong?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

所以你已经收集了数据，概述了业务案例，决定了候选模型（例如：随机森林），设置好了开发环境，双手已经准备好在键盘上开始。你准备好构建和训练你的时间序列模型了。

等一下——别急着开始。在训练和测试你的随机森林模型之前，你应该**先训练一个基准模型**。

# 什么是基准模型？

**基准模型**是一个简单的模型，用于创建一个基准或参考点，作为你构建最终更复杂的机器学习模型的基础。

数据科学家创建基准模型的原因：

+   基准模型可以让你大致了解更复杂的模型将如何表现。

+   如果基准模型表现不佳，这可能是数据质量存在问题的迹象，值得进行处理。

+   如果基准模型的表现优于最终模型，这可能表明该算法、特征、超参数或其他数据预处理存在问题。

+   如果基准模型和复杂模型的表现差不多，这可能意味着……
