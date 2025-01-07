# 滚动窗口在时间序列中的应用，使用 Python

> 原文：[`towardsdatascience.com/applications-of-rolling-windows-for-time-series-with-python-1a4bbe44901d?source=collection_archive---------0-----------------------#2024-09-15`](https://towardsdatascience.com/applications-of-rolling-windows-for-time-series-with-python-1a4bbe44901d?source=collection_archive---------0-----------------------#2024-09-15)

## 这是滚动窗口和时间序列的一些强大应用

[](https://piero-paialunga.medium.com/?source=post_page---byline--1a4bbe44901d--------------------------------)![Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--1a4bbe44901d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1a4bbe44901d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1a4bbe44901d--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--1a4bbe44901d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1a4bbe44901d--------------------------------) ·11 分钟阅读·2024 年 9 月 15 日

--

![](img/dc9bdab6704b09c75292476f60872e0e.png)

图片由[Claudia Aran](https://unsplash.com/@claudia_aran?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/water-drops-on-glass-window-L52QsKeNq40?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

昨晚我和妻子一起做**洗衣**。我们有一个*非言语*的约定（不过当我违反它时，它会变得相当*言语化*）：关于洗衣的事情，**她**负责把衣服放进**洗衣机**和**干衣机**，而**我**则负责**折叠**衣服。

我们通常是这样做的：

![](img/28d70f364cb75748f57fdd6f0965c7d1.png)

图像由作者使用 DALLE 制作

现在，我**并不是真的**折叠所有衣服并把它们收好。否则，我会被衣服淹没。我做的事情是一种让我想起**滚动窗口**方法的方式：

![](img/765f014344aca38ae5d7f25ef27f33be.png)

图像由作者使用 DALLE 制作

为什么我说它让我想起**滚动窗口**？让我们看看这个类比。

![](img/3e23126fadbead4e888eb59a3a7eb10e.png)

图像由作者使用 DALLE 制作

**滚动窗口**的概念正是我在折叠衣服时应用的那个方法。我有一个任务要做，但你…
