# 时间序列中的缺失数据：机器学习技术

> 原文：[https://towardsdatascience.com/missing-data-in-time-series-machine-learning-techniques-6b2273ff8b45?source=collection_archive---------4-----------------------#2024-12-10](https://towardsdatascience.com/missing-data-in-time-series-machine-learning-techniques-6b2273ff8b45?source=collection_archive---------4-----------------------#2024-12-10)

## 第1部分：利用线性回归和决策树填补时间序列中的缺失数据。

[](https://medium.com/@saranobregafn?source=post_page---byline--6b2273ff8b45--------------------------------)[![Sara Nóbrega](../Images/0cc9fafe4cfda6f38148d169b9055e29.png)](https://medium.com/@saranobregafn?source=post_page---byline--6b2273ff8b45--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6b2273ff8b45--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6b2273ff8b45--------------------------------) [Sara Nóbrega](https://medium.com/@saranobregafn?source=post_page---byline--6b2273ff8b45--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6b2273ff8b45--------------------------------) ·13分钟阅读·2024年12月10日

--

![](../Images/8586b2a55cfcc06c5358724ee88e49e8.png)

来源：DALL-E。

**（如果你没有订阅，请点击此处阅读此故事** [**这里**](/missing-data-in-time-series-machine-learning-techniques-6b2273ff8b45?sk=8082e7a98ee9ceef198243aee281086a)**）。**

**时间序列分析中的缺失数据** — 听起来很熟悉吗？

数据集中的缺失数据是不是由于传感器故障、数据传输问题或任何类型的维护而导致的，听起来很熟悉？

嗯，缺失值会破坏你的预测并扭曲你的分析。

**那么，你如何修复它们呢？**

**传统方法** 可能看起来是解决方案——前向填充或插值——**但这足够好吗**？

**当你的数据具有复杂的模式、非线性趋势或高度变化时，会发生什么？** 简单的技术将失败，并导致不稳定的结果。

**如果有更明智的方法来应对这个挑战呢？**

**机器学习正是如此：** 从回归分析到K最近邻再到神经网络，它们不假设任何内容，而是适应并精确地填补空白。

**好奇吗？** 让我们更深入地了解这些先进的方法如何改变你的时间序列分析。

> 我们将使用…填补缺失数据
