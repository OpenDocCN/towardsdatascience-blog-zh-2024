# Python中的因果推断与机器学习介绍

> 原文：[https://towardsdatascience.com/introduction-to-causal-inference-with-machine-learning-in-python-1a42f897c6ad?source=collection_archive---------0-----------------------#2024-01-24](https://towardsdatascience.com/introduction-to-causal-inference-with-machine-learning-in-python-1a42f897c6ad?source=collection_archive---------0-----------------------#2024-01-24)

## 探索在 Python 中应用的因果机器学习的概念和基本方法

[](https://medium.com/@marcopeixeiro?source=post_page---byline--1a42f897c6ad--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--1a42f897c6ad--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1a42f897c6ad--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1a42f897c6ad--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--1a42f897c6ad--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1a42f897c6ad--------------------------------) ·11分钟阅读·2024年1月24日

--

![](../Images/326ac79d4b694d8c8ed511698663dc9d.png)

图片由[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

因果推断在各种情境中有许多实际应用，但根据我的经验，它是一个在数据科学家中很少被讨论的话题。

在本文中，我们定义了因果推断并阐明了它的使用动机。然后，我们应用一些基本的Python算法来衡量某种现象的影响。

# 定义因果推断

因果推断是一个研究领域，旨在衡量某种治疗的效果。

另一种思考因果推断的方法是，它回答*假如*类的问题。目标始终是衡量某种行为所带来的影响。

用因果推断回答的问题示例包括：

+   广告活动对产品销量的影响是什么？

+   提价对销量的影响是什么？

+   这种药物能让病人更快康复吗？

我们可以看到，这些问题对于决策者来说非常相关，但无法通过传统的机器学习方法来解决。
