# 如何在卫星影像中检测洪水，案例研究：迪拜洪水

> 原文：[https://towardsdatascience.com/how-to-detect-floods-in-satellite-imagery-case-study-dubai-flooding-831cdf425a22?source=collection_archive---------5-----------------------#2024-04-24](https://towardsdatascience.com/how-to-detect-floods-in-satellite-imagery-case-study-dubai-flooding-831cdf425a22?source=collection_archive---------5-----------------------#2024-04-24)

## 使用简单的分类方法检测和监测卫星影像中的洪水区域

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--831cdf425a22--------------------------------)[![Mahyar Aboutalebi, Ph.D. 🎓](../Images/83d62352800f8a2932db8a07997c8059.png)](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--831cdf425a22--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--831cdf425a22--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--831cdf425a22--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--831cdf425a22--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--831cdf425a22--------------------------------) ·阅读时间 11 分钟 ·2024年4月24日

--

在周末，当我刷 Twitter 动态时，看到有关迪拜机场在一次罕见的暴风雨中被淹的新闻（24小时降水量超过 250 毫米！！）。我希望能够找到清晰的卫星图像，展示一种简单的方法来区分被淹和未被淹的区域。幸运的是，Sentinel-2 卫星在 4 月 7 日（洪水前）和 4 月 17 日（洪水后）拍摄了两幅几乎没有云的迪拜图像。这些图像激发了我写一篇关于使用卫星图像检测洪水事件的文章。

在这篇文章中，我们首先通过 Python 脚本下载了迪拜某洪水地区的 Sentinel-2 卫星影像。然后，我们将使用 rasterio 包读取影像，并利用近红外和绿色波段计算归一化差异水体指数（NDWI）。之后，我们将绘制洪水前后影像的 NDWI 直方图。通过比较这些直方图，我们可以看到洪水前的干旱区域如何转变为洪水后的湿润区域。最后，我们将通过从直方图分析中提取的阈值分离出被淹没的像素，并绘制洪水区域地图。如果这听起来很有趣，继续阅读吧！

# 目录

1.  🌅 **引言**

1.  💾 **下载 Sentinel-2 卫星影像**
