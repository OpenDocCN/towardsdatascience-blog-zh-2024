# 创建一个互动地图来显示卫星影像的时间序列

> 原文：[`towardsdatascience.com/create-an-interactive-map-to-display-time-series-of-satellite-imagery-e9346e165e27?source=collection_archive---------6-----------------------#2024-05-17`](https://towardsdatascience.com/create-an-interactive-map-to-display-time-series-of-satellite-imagery-e9346e165e27?source=collection_archive---------6-----------------------#2024-05-17)

## 学习如何使用 Folium 和 Plotly 库（Python）在地图上可视化来自卫星影像的时间序列数据

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--e9346e165e27--------------------------------)![Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--e9346e165e27--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e9346e165e27--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e9346e165e27--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--e9346e165e27--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e9346e165e27--------------------------------) ·11 分钟阅读·2024 年 5 月 17 日

--

![](img/8051e0ef34a1dbf6b20f61c0a6ac5964.png)

来源：ChatGPT 4-o

> 所有代码均使用 Python 编写，并在 Google Colab 中测试过。

# 目录

1.  **🌟 介绍**

1.  **📌 关注领域（AOI）**

1.  💾 **加载 Sentinel-2 卫星影像**

1.  ⏳ **从卫星影像中提取时间序列**

1.  🌍 **开发一个带有时间序列的互动地图**

1.  **📄 结论**

1.  **📚 参考文献**

## **🌟 介绍**

一段时间以来，我一直在寻找一种简单直观的方法，能够在用户点击特定位置时，创建一个显示时间序列数据的互动地图。我探索了 folium 库，并弄清楚了如何将从卫星影像中提取的时间序列数据库与 folium 连接，以实现这一目标。今天，我将分享我的方法。

在这篇文章中，我将编写两个函数。第一个函数在不下载卫星数据的情况下加载数据，第二个函数提取数据和时间戳，创建一个时间序列数据格式。然后…
