# 理解条件概率与贝叶斯定理

> 原文：[https://towardsdatascience.com/understanding-conditional-probability-and-bayes-theorem-b779f8801ef6?source=collection_archive---------1-----------------------#2024-07-18](https://towardsdatascience.com/understanding-conditional-probability-and-bayes-theorem-b779f8801ef6?source=collection_archive---------1-----------------------#2024-07-18)

![](../Images/a601b9d150959bed02fd6c67d7c81646.png)

由[Stephen Cobb](https://unsplash.com/@chuffed?utm_source=medium&utm_medium=referral)拍摄，图片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 介绍回归分析的两个基本概念

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--b779f8801ef6--------------------------------)[![Sachin Date](../Images/bd023298b414caf88f79b00ef032d065.png)](https://timeseriesreasoning.medium.com/?source=post_page---byline--b779f8801ef6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b779f8801ef6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b779f8801ef6--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--b779f8801ef6--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b779f8801ef6--------------------------------) ·阅读时长20分钟·2024年7月18日

--

历史上很少有事件能像1983年9月26日的事件那样，生动地展示条件概率是如何深深融入人类思维中的。

1983年9月26日午夜过后，一颗苏联[预警卫星](https://en.wikipedia.org/wiki/Early_warning_satellite)检测到来自美国的弹道导弹发射，目标指向苏联。

[斯坦尼斯拉夫·佩特罗夫中校](https://en.wikipedia.org/wiki/Stanislav_Petrov)，当时在莫斯科郊外一处秘密预警卫星控制中心值班，接收到了屏幕上的警报。他只有几分钟时间决定是否将信号标记为真实并通知上司。

该预警卫星系统专门设计用于检测弹道导弹发射。如果佩特罗夫告诉上司这个信号是真的，苏联领导层完全有理由产生恐慌。更复杂的是，1983年冷战局势已达到令人恐惧的高峰，这增强了苏联人认为来自预警卫星的信号可能确实是真实的概率。

关于佩特罗夫何时通知上司警报及两人之间交换了什么信息，记载有所不同，但有两点是确定的：
