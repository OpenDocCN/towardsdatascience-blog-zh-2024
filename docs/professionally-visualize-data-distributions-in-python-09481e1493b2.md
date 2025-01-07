# 在 Python 中专业地可视化数据分布

> 原文：[`towardsdatascience.com/professionally-visualize-data-distributions-in-python-09481e1493b2?source=collection_archive---------3-----------------------#2024-02-18`](https://towardsdatascience.com/professionally-visualize-data-distributions-in-python-09481e1493b2?source=collection_archive---------3-----------------------#2024-02-18)

## 学习七种不同的可视化数据分布的方法

[](https://medium.com/@kurt.klingensmith?source=post_page---byline--09481e1493b2--------------------------------)![Kurt Klingensmith](https://medium.com/@kurt.klingensmith?source=post_page---byline--09481e1493b2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--09481e1493b2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09481e1493b2--------------------------------) [Kurt Klingensmith](https://medium.com/@kurt.klingensmith?source=post_page---byline--09481e1493b2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09481e1493b2--------------------------------) ·阅读时间：12 分钟·2024 年 2 月 18 日

--

![](img/f14a06ee7db4f9cff79ec33a3b241b4c.png)

图片来源：[NEOM](https://unsplash.com/@neom?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/the-sun-is-setting-over-the-mountains-in-the-desert-9hWJs7iblh8?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)。

探索性数据分析和数据可视化通常包括检查数据集的分布情况。这样做可以提供对数据的重要见解，例如识别范围、异常值或不寻常的分组、数据的集中趋势以及数据中的偏斜。比较数据的子集可以揭示更多关于手头数据的信息。专业构建的数据分布可视化将提供即时见解。本指南详细介绍了几种快速使用 Python 创建这些清晰、有意义的可视化图表的选项。

## 涵盖的可视化：

+   直方图

+   KDE（密度）图

+   Joy 图或岭图

+   箱型图

+   小提琴图

+   条形图和聚类图

+   ECDF 图

## 数据与代码：

本文使用的是完全合成的天气数据，[该数据是基于我之前一篇文章中的概念生成的](https://medium.com/towards-data-science/build-your-own-synthetic-data-15d91389a37b)。本文的数据和完整的 Jupyter 笔记本可以在此[**GitHub 页面**](https://github.com/kurtklingensmith/DataDistributions/tree/main)下载。欢迎下载这两个文件并跟随操作，或者…
