# 评估时间序列中异常值处理影响的终极指南

> 原文：[https://towardsdatascience.com/evaluating-the-impact-of-outlier-treatment-in-time-series-b4fac4cabe94?source=collection_archive---------1-----------------------#2024-11-13](https://towardsdatascience.com/evaluating-the-impact-of-outlier-treatment-in-time-series-b4fac4cabe94?source=collection_archive---------1-----------------------#2024-11-13)

## 敏感性分析、模型验证、特征重要性等！

[](https://medium.com/@saranobregafn?source=post_page---byline--b4fac4cabe94--------------------------------)[![Sara Nóbrega](../Images/0cc9fafe4cfda6f38148d169b9055e29.png)](https://medium.com/@saranobregafn?source=post_page---byline--b4fac4cabe94--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4fac4cabe94--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b4fac4cabe94--------------------------------) [Sara Nóbrega](https://medium.com/@saranobregafn?source=post_page---byline--b4fac4cabe94--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4fac4cabe94--------------------------------) ·阅读时长19分钟·2024年11月13日

--

![](../Images/518a0a627552487ab49fac14a71bf7d8.png)

来源：DaLL-E。

*(如果你没有会员资格，请阅读本文* [*点击这里)*](/evaluating-the-impact-of-outlier-treatment-in-time-series-b4fac4cabe94?sk=ccd20bf556ad7d8ad7e8557b2d513d6e)*.*

**设想一下：** 你正在处理时间序列数据，寻找模式并调查随时间变化的趋势。

你已经对你的时间序列数据进行了**探索性数据分析**，并且已经寻找了最佳的**异常值检测方法**。

在检测之后，无论是忽略它们、删除它们，还是更常见的情况是，**你已经对它们进行了转换**。

现在是时候**评估这种处理的影响**了：你的数据分布发生了什么变化？你的机器学习模型在预测目标变量时效果如何？

此外，人们可能会好奇：

+   **你将使用哪些指标来评估模型的表现？**

+   **你将如何可视化数据分布的变化？**

+   **哪些因素可能影响了你模型的预测？**

+   **数据中是否存在任何可能影响评估的偏差？**

> 我们将通过以下方法回答其中的一些问题…
