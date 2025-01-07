# 使用自编码器进行时间序列异常检测，Python 实战

> 原文：[`towardsdatascience.com/hands-on-time-series-anomaly-detection-using-autoencoders-with-python-7cd893bbc122?source=collection_archive---------3-----------------------#2024-08-21`](https://towardsdatascience.com/hands-on-time-series-anomaly-detection-using-autoencoders-with-python-7cd893bbc122?source=collection_archive---------3-----------------------#2024-08-21)

## 这里是如何用几行代码使用自编码器来检测含有异常的信号

[](https://piero-paialunga.medium.com/?source=post_page---byline--7cd893bbc122--------------------------------)![Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--7cd893bbc122--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7cd893bbc122--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7cd893bbc122--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--7cd893bbc122--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7cd893bbc122--------------------------------) ·10 分钟阅读·2024 年 8 月 21 日

--

![](img/251a13a7ec986cf0706c0a18822430a1.png)

图片由[davisuko](https://unsplash.com/@davisuko?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/blue-lemon-sliced-into-two-halves-5E5N49RWtbA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

异常时间序列是一个非常严肃的问题。

如果你想到**地震**，**异常**就是数据中突然的峰值或下降的**地震信号**，这些信号暗示着某些不好的事情正在发生。

在**金融**数据中，大家都记得 1929 年的[华尔街崩盘](https://en.wikipedia.org/wiki/Wall_Street_Crash_of_1929)，那是金融领域中一个信号**带有异常**的明显例子。在**工程学**中，信号中的尖峰可能代表超声波反射到墙壁或人的情况。

所有这些故事都源于一个非常明确的问题：

> 如果我有一组正常信号，并且有一个新的信号进入，我该如何检测该信号是否异常？

请注意，这个问题与**检测给定信号中的异常**的问题略有不同（这个问题也是一个著名的待解决问题）。在这种情况下，我们假设得到一个全新的信号，并且我们想知道**该信号是否与我们数据集中被认为是“正常”的信号有显著差异**。
