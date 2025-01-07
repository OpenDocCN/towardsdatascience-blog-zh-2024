# 将卫星热图像从1000米缩放到10米（Python）

> 原文：[https://towardsdatascience.com/downscaling-a-satellite-thermal-image-from-1000-m-to-10-m-python-3b2ed19ff103?source=collection_archive---------1-----------------------#2024-03-06](https://towardsdatascience.com/downscaling-a-satellite-thermal-image-from-1000-m-to-10-m-python-3b2ed19ff103?source=collection_archive---------1-----------------------#2024-03-06)

## Sentinel-3图像的热锐化：使用Python在Google Colab中将1公里图像锐化至10米

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--3b2ed19ff103--------------------------------)[![Mahyar Aboutalebi, Ph.D. 🎓](../Images/83d62352800f8a2932db8a07997c8059.png)](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--3b2ed19ff103--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b2ed19ff103--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3b2ed19ff103--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--3b2ed19ff103--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b2ed19ff103--------------------------------) ·13分钟阅读·2024年3月6日

--

![](../Images/ea0c419c5913c8f66ff30f64c7f69613.png)

Sentinel-3热图像从1000米缩放到10米，由作者可视化。

# 目录

1.  🌅 **简介**

1.  💾 **下载Sentinel-3（1000米）和Sentinel-2图像（10米）**

1.  ⚙️ **Sentinel-3图像处理**

1.  🌡️ **温度-NDVI空间**

1.  📐 **热图像锐化（从1000米到10米）**

1.  🗺️ **锐化热图像的可视化**

1.  **📄 结论**

1.  **📚 参考资料**

## 🌅 **简介**

卫星捕获的热图像的下采样已被广泛研究，原因在于提供热图像的卫星在空间分辨率和时间分辨率之间存在权衡。例如，Landsat-8的重访周期为16天，原始热分辨率为100米。相比之下，Sentinel-3每天可以提供热图像，但空间分辨率为1000米。

![](../Images/2b88d573d20e6a76e008d205230cbc40.png)

空间与时间分辨率之间的权衡，图像由作者提供
