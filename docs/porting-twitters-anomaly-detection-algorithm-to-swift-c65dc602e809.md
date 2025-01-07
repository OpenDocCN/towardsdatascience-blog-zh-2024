# 将 Twitter 的异常检测算法移植到 Swift

> 原文：[`towardsdatascience.com/porting-twitters-anomaly-detection-algorithm-to-swift-c65dc602e809?source=collection_archive---------6-----------------------#2024-11-29`](https://towardsdatascience.com/porting-twitters-anomaly-detection-algorithm-to-swift-c65dc602e809?source=collection_archive---------6-----------------------#2024-11-29)

## 从 Twitter 到 Swift：构建异常检测。

[](https://aaronbeckley.medium.com/?source=post_page---byline--c65dc602e809--------------------------------)![Aaron Beckley](https://aaronbeckley.medium.com/?source=post_page---byline--c65dc602e809--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c65dc602e809--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c65dc602e809--------------------------------) [Aaron Beckley](https://aaronbeckley.medium.com/?source=post_page---byline--c65dc602e809--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c65dc602e809--------------------------------) ·12 分钟阅读·2024 年 11 月 29 日

--

![](img/278f83f7ddf05aeec9befb10a6d182e5.png)

[我 ADS 应用的放大图](https://github.com/ambeckley/Anomaly-Detection-App-Ios).

Twitter（现为 X），在 2015 年[开发了一种异常检测算法，用于追踪其数百万用户之间的趋势](https://blog.x.com/engineering/en_us/a/2015/introducing-practical-and-robust-anomaly-detection-in-a-time-series)

[](https://github.com/twitter/AnomalyDetection?source=post_page-----c65dc602e809--------------------------------) [## GitHub - twitter/AnomalyDetection：使用 R 进行异常检测

### 使用 R 进行异常检测。通过在 GitHub 上创建账户，贡献于 twitter/AnomalyDetection 的开发。

[github.com](https://github.com/twitter/AnomalyDetection?source=post_page-----c65dc602e809--------------------------------)

这个完全使用 R 语言制作的软件包仍然非常实用。[它被设计用于检测全球性和局部性的异常](https://anomaly.io/anomaly-detection-twitter-r/index.html)，并且能够成功地检测各种异常。有关它能检测和不能检测的内容，请查看[Anomaly.io 对原始算法的测试](https://anomaly.io/anomaly-detection-twitter-r/index.html)，它非常全面。

# **为什么要移植到 Swift？**

为什么不呢 🤷‍♂️？我感到无聊。

# **理解 Twitter 的异常检测算法**

Twitter 的异常检测算法是一个统计框架，旨在检测时间序列数据集中的异常值或离群点。

该算法有两个主要的核心组成部分。

+   **季节性分解**：该算法...
