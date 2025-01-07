# 使用贝叶斯建模预测欧冠联赛

> 原文：[https://towardsdatascience.com/using-bayesian-modeling-to-predict-the-champions-league-8ebb069006ba?source=collection_archive---------1-----------------------#2024-02-20](https://towardsdatascience.com/using-bayesian-modeling-to-predict-the-champions-league-8ebb069006ba?source=collection_archive---------1-----------------------#2024-02-20)

## 体育分析

## 贝叶斯推断在现实世界中的应用

[](https://polmarin.medium.com/?source=post_page---byline--8ebb069006ba--------------------------------)[![Pol Marin](../Images/a4f69a96717d453db9791f27b8f85e86.png)](https://polmarin.medium.com/?source=post_page---byline--8ebb069006ba--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8ebb069006ba--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8ebb069006ba--------------------------------) [Pol Marin](https://polmarin.medium.com/?source=post_page---byline--8ebb069006ba--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8ebb069006ba--------------------------------) ·15分钟阅读·2024年2月20日

--

![](../Images/c0ec9b4f73faaa9545dceb150ef11d16.png)

图片由[Anders Krøgh Jørgensen](https://unsplash.com/@anders_kj1?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

哦，欧冠联赛。可能是吸引最多球迷的比赛，无论他们支持哪个球队。这里是最强对最强的较量。比赛几乎可以保证精彩……而结果几乎无法预测。

但这不应该阻止我们去尝试。

前几天我在翻看旧的大学作业时，发现了一篇激发我写这篇文章的评估报告，我们将利用贝叶斯推断创建一个模型，尝试预测接下来的欧冠联赛比赛：16强的首回合（说实话，这个模型也可以用来预测任何阶段的比赛）。

目标是通过贝叶斯统计在一个我认为既有趣又有娱乐性的实际案例中建立一个模型。

无论你是否了解贝叶斯推断，这篇文章都是为你准备的。即使你已经了解我接下来要分享的所有内容，最终的预测至少能让你在首回合比赛结束后，或者恭喜我，或者嘲笑我。

今天我们将讨论以下内容：

+   贝叶斯推断与建模
