# 加泰罗尼亚语言灭绝建模

> 原文：[https://towardsdatascience.com/modeling-the-extinction-of-the-catalan-language-01eda1527f5d?source=collection_archive---------9-----------------------#2024-06-28](https://towardsdatascience.com/modeling-the-extinction-of-the-catalan-language-01eda1527f5d?source=collection_archive---------9-----------------------#2024-06-28)

## 将现有文献应用于实际案例

[](https://polmarin.medium.com/?source=post_page---byline--01eda1527f5d--------------------------------)[![Pol Marin](../Images/a4f69a96717d453db9791f27b8f85e86.png)](https://polmarin.medium.com/?source=post_page---byline--01eda1527f5d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--01eda1527f5d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--01eda1527f5d--------------------------------) [Pol Marin](https://polmarin.medium.com/?source=post_page---byline--01eda1527f5d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--01eda1527f5d--------------------------------) ·9分钟阅读·2024年6月28日

--

![](../Images/21dba3761d4913aefc8562520491c987.png)

图片来自[Brett Jordan](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们能预测一种语言的灭绝吗？这听起来并不简单，实际上也不应该简单，但这并不应该阻止我们尝试建立模型。

最近我对这个话题产生了兴趣，开始回顾一些现有的文献。我遇到了一篇文章[1]，我很喜欢，决定与大家分享。

因此，在这篇文章中，我将分享那篇论文的见解，并将其翻译成（希望）易于理解的内容，并应用到一个实际案例中，以便我们能看到数据科学和数学建模的实际应用。

## 介绍

我是加泰罗尼亚人，对于那些不了解的人，加泰罗尼亚语是加泰罗尼亚、瓦伦西亚自治区和巴利阿里群岛（西班牙）与西班牙语共同的官方语言。它还是安道尔的官方语言，并在法国南部和意大利的阿尔盖罗（Alghero）地区存在。

我们常在当地的电视或媒体上看到，加泰罗尼亚语面临灭绝的风险。仅仅聚焦于加泰罗尼亚，我们就能深入了解这个情况，因为政府通过所谓的“语言使用调查”（Enquesta d’usos lingüistics de la població）来研究语言的使用情况[2]。
