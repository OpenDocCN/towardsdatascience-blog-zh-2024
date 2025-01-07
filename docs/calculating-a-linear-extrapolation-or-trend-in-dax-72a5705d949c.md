# 在DAX中计算线性外推（或趋势）

> 原文：[https://towardsdatascience.com/calculating-a-linear-extrapolation-or-trend-in-dax-72a5705d949c?source=collection_archive---------5-----------------------#2024-12-26](https://towardsdatascience.com/calculating-a-linear-extrapolation-or-trend-in-dax-72a5705d949c?source=collection_archive---------5-----------------------#2024-12-26)

## *计算趋势帮助我们识别我们的业务是否朝着正确的方向发展。虽然其他编程语言可以提供内置功能来计算这一点，但DAX没有，我们必须自己动手。*

[](https://medium.com/@salvatorecagliari?source=post_page---byline--72a5705d949c--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--72a5705d949c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--72a5705d949c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--72a5705d949c--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--72a5705d949c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--72a5705d949c--------------------------------) ·阅读时间9分钟·2024年12月26日

--

![](../Images/a0413d6aae660375ba0aa43b5523c20c.png)

图片由[Adam Nowakowski](https://unsplash.com/@adamaszczos?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

曾经有一位客户要求我计算他数据的线性外推，以便根据过去的数据展示趋势。

这介于[描述性和预测性分析](https://en.wikipedia.org/wiki/Prescriptive_analytics)之间，因为它不使用机器学习或AI技术。

它不会考虑数据中的[季节性](https://medium.com/@dbhatt245/understanding-seasonality-in-time-series-data-1bd878fe86bf)或其他影响因素。

这里描述的方法使用现有数据，并线性计算所有后续月份、季度或其他时期的外推。

让我们深入了解一下。

# 数据和场景

使用著名的Contoso零售数据（数据来源见下文的参考部分），我想分析购买我公司产品的客户数量。

假设当前日期是2022年4月，我看到以下是销售第一季度的数据……
