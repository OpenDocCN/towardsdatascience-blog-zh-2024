# 发现时间序列数据中的异常值终极指南（第一部分）

> 原文：[https://towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?source=collection_archive---------0-----------------------#2024-05-21](https://towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?source=collection_archive---------0-----------------------#2024-05-21)

## 时间序列分析中的有效统计方法和工具用于检测异常值

[](https://medium.com/@saranobregafn?source=post_page---byline--1bf81e09ade4--------------------------------)[![Sara Nóbrega](../Images/0cc9fafe4cfda6f38148d169b9055e29.png)](https://medium.com/@saranobregafn?source=post_page---byline--1bf81e09ade4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1bf81e09ade4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1bf81e09ade4--------------------------------) [Sara Nóbrega](https://medium.com/@saranobregafn?source=post_page---byline--1bf81e09ade4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1bf81e09ade4--------------------------------) ·阅读时间 18 分钟 ·2024年5月21日

--

![](../Images/084a852b02fa1164728777478dd29184.png)

来源：[DALL·E](https://openai.com/index/dall-e-2/)

*如果你不是 Medium 的会员，你可以在这里* [*阅读文章*](https://medium.com/towards-data-science/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?sk=215c6094b7bdc43812933df4c94d8cc7)*.*

**异常值：** 那些麻烦的数据点，它们可能会扭曲统计模型、扭曲预测结果，并破坏决策过程。

难怪它们在数据分析中不太受欢迎。

我叫Sara，我拥有物理学硕士学位。目前，我在一家全球能源公司担任数据科学家。

本文是一个**四部分系列**的开始，专注于**时间序列数据中异常值的识别和管理**。如果你有兴趣跟随这个系列，请确保[**关注我**](https://medium.com/@saranobregafn) **并** [**订阅**](https://medium.com/@saranobregafn/subscribe)，以便在下一部分发布时收到更新！

在这篇**初步文章**中，我们探讨了**视觉和统计方法**，以有效识别时间序列数据中的异常值。这些基础知识对于任何想要提高分析准确性的人来说都是至关重要的。

**在第二篇文章中**，我将专门讨论**机器学习方法**，由于其重要性和复杂性，它们值得单独讨论：
