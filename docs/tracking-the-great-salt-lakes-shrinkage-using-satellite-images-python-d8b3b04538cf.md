# 使用卫星图像跟踪大盐湖的缩小（Python）

> 原文：[`towardsdatascience.com/tracking-the-great-salt-lakes-shrinkage-using-satellite-images-python-d8b3b04538cf?source=collection_archive---------8-----------------------#2024-02-28`](https://towardsdatascience.com/tracking-the-great-salt-lakes-shrinkage-using-satellite-images-python-d8b3b04538cf?source=collection_archive---------8-----------------------#2024-02-28)

## 分析 Landsat-8 影像（2014–2023）并使用 Python 在 Google Colab 中创建大盐湖表面积的时间序列

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--d8b3b04538cf--------------------------------)![Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--d8b3b04538cf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d8b3b04538cf--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d8b3b04538cf--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--d8b3b04538cf--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d8b3b04538cf--------------------------------) ·17 分钟阅读·2024 年 2 月 28 日

--

![](img/b531bf32d722dc2e92699ee58b49dc3b.png)

大盐湖地区的时间流逝影像，基于 2014 年至 2023 年的无云 Landsat-8 图像，由作者可视化

# 目录

1.  🌅 **大盐湖缩小问题简介**

1.  💾 **下载 Landsat-8 图像**

1.  📈 **来自统计文件的大盐湖地区时间序列**

1.  ⚙️ **处理 Landsat-8 图像**

1.  🗺️ **大盐湖图像的可视化**

1.  **🎥 大盐湖缩小的时间流逝影像**

1.  📉 **基于分类图像的大盐湖地区时间序列**

1.  ⚖️ **比较统计文件和图像中的时间序列**

1.  **📄 结论**

1.  **📚 参考文献**

## 🌅 **大盐湖缩小问题简介**

美国犹他州的大盐湖正在经历长期缩小。根据多项报告，与 1986 年最大水面相比，湖泊已缩小超过 30%。关于湖泊水位下降，有几个原因被提及……
