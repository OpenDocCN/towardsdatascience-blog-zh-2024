# 将Twitter的异常检测算法移植到Swift

> 原文：[https://towardsdatascience.com/porting-twitters-anomaly-detection-algorithm-to-swift-c65dc602e809?source=collection_archive---------6-----------------------#2024-11-29](https://towardsdatascience.com/porting-twitters-anomaly-detection-algorithm-to-swift-c65dc602e809?source=collection_archive---------6-----------------------#2024-11-29)

## 从Twitter到Swift：构建异常检测。

[](https://aaronbeckley.medium.com/?source=post_page---byline--c65dc602e809--------------------------------)[![Aaron Beckley](../Images/673834b61e5786d207cd7deb39c12d87.png)](https://aaronbeckley.medium.com/?source=post_page---byline--c65dc602e809--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c65dc602e809--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c65dc602e809--------------------------------) [Aaron Beckley](https://aaronbeckley.medium.com/?source=post_page---byline--c65dc602e809--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c65dc602e809--------------------------------) ·12分钟阅读·2024年11月29日

--

![](../Images/278f83f7ddf05aeec9befb10a6d182e5.png)

[我ADS应用的放大图](https://github.com/ambeckley/Anomaly-Detection-App-Ios).

Twitter（现为X），在2015年[开发了一种异常检测算法，用于追踪其数百万用户之间的趋势](https://blog.x.com/engineering/en_us/a/2015/introducing-practical-and-robust-anomaly-detection-in-a-time-series)

[](https://github.com/twitter/AnomalyDetection?source=post_page-----c65dc602e809--------------------------------) [## GitHub - twitter/AnomalyDetection：使用R进行异常检测

### 使用R进行异常检测。通过在GitHub上创建账户，贡献于twitter/AnomalyDetection的开发。

[github.com](https://github.com/twitter/AnomalyDetection?source=post_page-----c65dc602e809--------------------------------)

这个完全使用R语言制作的软件包仍然非常实用。[它被设计用于检测全球性和局部性的异常](https://anomaly.io/anomaly-detection-twitter-r/index.html)，并且能够成功地检测各种异常。有关它能检测和不能检测的内容，请查看[Anomaly.io对原始算法的测试](https://anomaly.io/anomaly-detection-twitter-r/index.html)，它非常全面。

# **为什么要移植到Swift？**

为什么不呢 🤷‍♂️？我感到无聊。

# **理解Twitter的异常检测算法**

Twitter的异常检测算法是一个统计框架，旨在检测时间序列数据集中的异常值或离群点。

该算法有两个主要的核心组成部分。

+   **季节性分解**：该算法...
