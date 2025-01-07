# 将卫星热图像从 1000 米缩放到 10 米（Python）

> 原文：[`towardsdatascience.com/downscaling-a-satellite-thermal-image-from-1000-m-to-10-m-python-3b2ed19ff103?source=collection_archive---------1-----------------------#2024-03-06`](https://towardsdatascience.com/downscaling-a-satellite-thermal-image-from-1000-m-to-10-m-python-3b2ed19ff103?source=collection_archive---------1-----------------------#2024-03-06)

## Sentinel-3 图像的热锐化：使用 Python 在 Google Colab 中将 1 公里图像锐化至 10 米

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--3b2ed19ff103--------------------------------)![Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--3b2ed19ff103--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b2ed19ff103--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b2ed19ff103--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--3b2ed19ff103--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b2ed19ff103--------------------------------) ·13 分钟阅读·2024 年 3 月 6 日

--

![](img/ea0c419c5913c8f66ff30f64c7f69613.png)

Sentinel-3 热图像从 1000 米缩放到 10 米，由作者可视化。

# 目录

1.  🌅 **简介**

1.  💾 **下载 Sentinel-3（1000 米）和 Sentinel-2 图像（10 米）**

1.  ⚙️ **Sentinel-3 图像处理**

1.  🌡️ **温度-NDVI 空间**

1.  📐 **热图像锐化（从 1000 米到 10 米）**

1.  🗺️ **锐化热图像的可视化**

1.  **📄 结论**

1.  **📚 参考资料**

## 🌅 **简介**

卫星捕获的热图像的下采样已被广泛研究，原因在于提供热图像的卫星在空间分辨率和时间分辨率之间存在权衡。例如，Landsat-8 的重访周期为 16 天，原始热分辨率为 100 米。相比之下，Sentinel-3 每天可以提供热图像，但空间分辨率为 1000 米。

![](img/2b88d573d20e6a76e008d205230cbc40.png)

空间与时间分辨率之间的权衡，图像由作者提供
