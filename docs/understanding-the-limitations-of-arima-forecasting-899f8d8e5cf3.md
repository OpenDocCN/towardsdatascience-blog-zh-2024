# 理解 ARIMA 预测的局限性

> 原文：[`towardsdatascience.com/understanding-the-limitations-of-arima-forecasting-899f8d8e5cf3?source=collection_archive---------5-----------------------#2024-08-21`](https://towardsdatascience.com/understanding-the-limitations-of-arima-forecasting-899f8d8e5cf3?source=collection_archive---------5-----------------------#2024-08-21)

## SARIMA 模型与 Facebook Prophet 模型的比较

[](https://medium.com/@khinydnlin_310?source=post_page---byline--899f8d8e5cf3--------------------------------)![Khin Yadanar Lin](https://medium.com/@khinydnlin_310?source=post_page---byline--899f8d8e5cf3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--899f8d8e5cf3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--899f8d8e5cf3--------------------------------) [Khin Yadanar Lin](https://medium.com/@khinydnlin_310?source=post_page---byline--899f8d8e5cf3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--899f8d8e5cf3--------------------------------) ·12 分钟阅读·2024 年 8 月 21 日

--

![](img/75490f81c26a6ef5e237a12a6b2c238f.png)

图片由[Maksym Ostrozhynskyy](https://unsplash.com/@maxon?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 目录

+   介绍

+   ARIMA 概述

+   ARIMA 模型的关键假设与局限性

+   Python 中的预测过程：季节性 ARIMA 与 Facebook Prophet

+   两种模型的性能比较

+   结论

# 介绍

时间序列预测长期以来一直是商业中预测的一个重要工具，尤其是在机器学习（ML）兴起之前。它在资源分配、库存管理和预算规划等领域扮演着重要角色。通常，预测被描述为艺术与科学的结合，因为它既需要技术理解，又需要人类的直觉和判断力。

在时间序列预测技术中，ARIMA（自回归积分滑动平均）是最受欢迎的方法之一，最早由 George Box 和 Gwilym Jenkins 于 1970 年开发。然而，尽管 ARIMA 很受欢迎，但它也存在一些缺点。本文旨在比较 ARIMA 与 Facebook 开发的 Prophet 模型的表现，并突出其…
