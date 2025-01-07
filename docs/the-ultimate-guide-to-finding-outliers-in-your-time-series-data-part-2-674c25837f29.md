# 《终极指南：如何在时间序列数据中发现异常值（第二部分）》

> 原文：[https://towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-2-674c25837f29?source=collection_archive---------3-----------------------#2024-06-26](https://towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-2-674c25837f29?source=collection_archive---------3-----------------------#2024-06-26)

## 时间序列分析中用于异常值检测的有效机器学习方法与工具

[](https://medium.com/@saranobregafn?source=post_page---byline--674c25837f29--------------------------------)[![Sara Nóbrega](../Images/0cc9fafe4cfda6f38148d169b9055e29.png)](https://medium.com/@saranobregafn?source=post_page---byline--674c25837f29--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--674c25837f29--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--674c25837f29--------------------------------) [Sara Nóbrega](https://medium.com/@saranobregafn?source=post_page---byline--674c25837f29--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--674c25837f29--------------------------------) ·18分钟阅读·2024年6月26日

--

![](../Images/623d12bb38e09d87ae68c26aae939d87.png)

图片由作者提供。

如果您不是Medium的会员，您可以在[这里](https://towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-2-674c25837f29?source=collection_archive---------3-----------------------#2024-06-26)阅读该文章。

**异常值：**那些可能会干扰统计模型、误导预测并扰乱决策过程的数据点。

本文**是**一系列**四篇文章中的第二篇**，专注于**时间序列数据中异常值的识别与管理**。

本系列的[第一篇文章](/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?sk=215c6094b7bdc43812933df4c94d8cc7)探讨了如何有效地通过**可视化和统计方法**来识别时间序列数据中的异常值：

[](/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?source=post_page-----674c25837f29--------------------------------) [## 《终极指南：如何在时间序列数据中发现异常值（第一部分）》

### 时间序列分析中用于异常值检测的有效统计方法与工具

towardsdatascience.com](/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?source=post_page-----674c25837f29--------------------------------)

**本文第二部分**将专门讨论**用于异常值检测的机器学习方法**。

鉴于它们的重要性和复杂性，值得进行专门的讨论！

**在找到离群值之后**，你应该做什么？**第三篇文章**探讨了**如何** **管理** **这些离群值**的各种策略，包括…
