# 在pandas中介绍时间序列

> 原文：[https://towardsdatascience.com/introducing-time-series-in-pandas-09c7cab569b4?source=collection_archive---------5-----------------------#2024-05-20](https://towardsdatascience.com/introducing-time-series-in-pandas-09c7cab569b4?source=collection_archive---------5-----------------------#2024-05-20)

## 快速成功的数据科学

## 处理日期和时间的初学者指南

[](https://medium.com/@lee_vaughan?source=post_page---byline--09c7cab569b4--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--09c7cab569b4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--09c7cab569b4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--09c7cab569b4--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--09c7cab569b4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09c7cab569b4--------------------------------) ·阅读时间：25分钟·2024年5月20日

--

![](../Images/af32a44584ff0a970b2af4a301f075d2.png)

Panda时间，来自DALL-E3

参考时间索引的数据称为*时间序列*。可以想象一下股票价格、温度记录和Google趋势。虽然我不认识喜欢处理时间序列的人，但Python尽力让这一过程尽可能简单。

Python及其pandas数据分析库将日期和时间视为特殊对象，这些对象“了解”公历、六十进制（60进制）时间系统、时区、夏令时、闰年等的机制。Python本身通过其`[datetime](https://docs.python.org/3/library/datetime.html)`模块支持时间序列，而pandas则专注于使用日期数组，例如作为DataFrame中的索引或列。

在这篇*快速成功的数据科学*文章中，我们将回顾pandas如何处理日期和时间。目标是向你介绍如何在pandas中处理时间序列的基础知识，并使你能够熟练掌握这一主题。如需更详细的内容，请访问pandas的[用户指南](https://pandas.pydata.org/docs/user_guide/timeseries.html)。如果你需要安装pandas，请参考[入门指南](https://pandas.pydata.org/docs/getting_started/index.html)。

# pandas中的时间概念

正如你可能预料的，pandas具有处理时间序列的广泛能力。这些功能基于NumPy的`datetime64`……
