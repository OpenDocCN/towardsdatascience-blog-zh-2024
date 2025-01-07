# 处理时间序列中的缺口

> 原文：[https://towardsdatascience.com/handling-gaps-in-time-series-dc47ae883990?source=collection_archive---------4-----------------------#2024-01-31](https://towardsdatascience.com/handling-gaps-in-time-series-dc47ae883990?source=collection_archive---------4-----------------------#2024-01-31)

## 短序列和长序列插补的缺失性分析与评估方法

[](https://medium.com/@erich.hs?source=post_page---byline--dc47ae883990--------------------------------)[![Erich Silva](../Images/448dee1644d3f3e092bbbcfbbf07592d.png)](https://medium.com/@erich.hs?source=post_page---byline--dc47ae883990--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc47ae883990--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dc47ae883990--------------------------------) [Erich Silva](https://medium.com/@erich.hs?source=post_page---byline--dc47ae883990--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc47ae883990--------------------------------) ·阅读时间 22分钟·2024年1月31日

--

![](../Images/fa54bdfffac3a18bfd84af6520b53324.png)

由[Willian Justen de Vasconcellos](https://unsplash.com/@willianjusten?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，图片来源于[Unsplash](https://unsplash.com/photos/landscape-photography-of-desert-jOF2TFSNxQI?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

时间是物理学中最明确的连续体，因此在自然界中也如此。正因如此，时间序列数据集中的连续性——即一系列按时间顺序排列的观察——显得尤为重要。

这个概念本身驱动了本文的动机。现实世界中的数据集由于各种原因容易出现缺失值，比如传感器故障、数据采集失败，或是在某个特定时间段内信息的缺失。然而，这并不会改变你特征的数据生成过程的基本性质。

因此，理解那些中断的原因，并在时间序列数据集中进行分析与处理，对于后续任务至关重要。

## 目录

+   [本文目标](#58de)

+   [数据集描述](#f3ff)

+   [库与依赖项](#9dee)

+   [数据预处理](#3ade)

+   [孤立缺失值与连续缺失值](#4f45)

+   [缺失可视化](#a0e1)

+   [为实验创建子集区间](http://5ebb)

+   [人为缺失数据](#1639)
