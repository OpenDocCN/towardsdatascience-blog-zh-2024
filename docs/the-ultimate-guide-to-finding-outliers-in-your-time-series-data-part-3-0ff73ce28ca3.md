# 《发现时间序列数据中的异常值终极指南（第三部分）》

> 原文：[`towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-3-0ff73ce28ca3?source=collection_archive---------4-----------------------#2024-07-16`](https://towardsdatascience.com/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-3-0ff73ce28ca3?source=collection_archive---------4-----------------------#2024-07-16)

## 已找到异常值：接下来怎么办？异常值处理指南

[](https://medium.com/@saranobregafn?source=post_page---byline--0ff73ce28ca3--------------------------------)![Sara Nóbrega](https://medium.com/@saranobregafn?source=post_page---byline--0ff73ce28ca3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0ff73ce28ca3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0ff73ce28ca3--------------------------------) [Sara Nóbrega](https://medium.com/@saranobregafn?source=post_page---byline--0ff73ce28ca3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0ff73ce28ca3--------------------------------) ·15 分钟阅读·2024 年 7 月 16 日

--

![](img/8eb7a253f725264a6bb3aa89a535d26b.png)

来源：[DALL·E](https://openai.com/index/dall-e-2/)

*如果你不是会员，可以在这里阅读* *本文。*

**异常值：**那些可能影响统计模型、误导预测并破坏决策过程的数据点。

**异常值处理**是数据分析中的必要步骤，许多人觉得它具有挑战性。

通过本文的见解，我希望能够简化这一过程，并使其更加有趣！

本文是关于**时间序列数据中异常值的识别与管理**的**四篇系列文章**中的**第三篇**。

本系列的**第一篇文章**讨论了如何有效地使用**视觉方法和统计方法**来识别时间序列数据中的异常值：

[](/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-1-1bf81e09ade4?source=post_page-----0ff73ce28ca3--------------------------------) ## 发现时间序列数据中的异常值终极指南（第一部分）

### 时间序列分析中异常值检测的有效统计方法和工具

towardsdatascience.com

在[**第二篇文章**](https://medium.com/towards-data-science/the-ultimate-guide-to-finding-outliers-in-your-time-series-data-part-2-674c25837f29)中，我们探索了几种**机器学习技术**来识别异常值：
