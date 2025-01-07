# 利用纬度和经度进行特征工程

> 原文：[https://towardsdatascience.com/feature-engineering-with-latitude-and-longitude-2577b7ad7a45?source=collection_archive---------4-----------------------#2024-03-26](https://towardsdatascience.com/feature-engineering-with-latitude-and-longitude-2577b7ad7a45?source=collection_archive---------4-----------------------#2024-03-26)

## 利用你的地理空间数据的力量 —— 使用代码！

[](https://medium.com/@benfeifke?source=post_page---byline--2577b7ad7a45--------------------------------)[![Ben Feifke](../Images/dd4eb09e404dbd3a478ea1049cf17c90.png)](https://medium.com/@benfeifke?source=post_page---byline--2577b7ad7a45--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2577b7ad7a45--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2577b7ad7a45--------------------------------) [Ben Feifke](https://medium.com/@benfeifke?source=post_page---byline--2577b7ad7a45--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2577b7ad7a45--------------------------------) ·阅读时间 9 分钟·2024年3月26日

--

当今许多最具竞争力的科技市场涉及地图上的移动点：打车服务（Uber、Lyft、Grab）、微型出行服务（Lime、Bird）、食品配送服务（Delivery Hero、Postmates、Doordash）等。此外，许多不将客户位置作为其产品用例核心的服务，仍然希望了解客户的位置，以便根据客户所在的位置和周围发生的事情，更好地个性化他们的体验。

这对数据科学家来说意味着，我们的数据湖中充斥着大量的纬度和经度（双关语 intended）；而仅仅这两个变量中就蕴藏着丰富的信息！

创造性且有效地利用纬度和经度可以为我们的机器学习应用带来巨大的预测能力，并为我们的分析工作增加维度，帮助我们数据科学家为公司和客户创造更多价值。

本文的目标是展示几种仅使用纬度和经度的特征工程技术，并比较它们在迈阿密房屋销售价格预测问题上的预测能力。结构如下：

1.  迈阿密房屋销售价格预测问题设置

1.  特征工程实验

    2.1\. 原始纬度和经度

    2.2…
