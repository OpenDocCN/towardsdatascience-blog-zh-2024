# 使用卫星图像跟踪大盐湖的缩小（Python）

> 原文：[https://towardsdatascience.com/tracking-the-great-salt-lakes-shrinkage-using-satellite-images-python-d8b3b04538cf?source=collection_archive---------8-----------------------#2024-02-28](https://towardsdatascience.com/tracking-the-great-salt-lakes-shrinkage-using-satellite-images-python-d8b3b04538cf?source=collection_archive---------8-----------------------#2024-02-28)

## 分析Landsat-8影像（2014–2023）并使用Python在Google Colab中创建大盐湖表面积的时间序列

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--d8b3b04538cf--------------------------------)[![Mahyar Aboutalebi, Ph.D. 🎓](../Images/83d62352800f8a2932db8a07997c8059.png)](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--d8b3b04538cf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d8b3b04538cf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d8b3b04538cf--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--d8b3b04538cf--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d8b3b04538cf--------------------------------) ·17分钟阅读·2024年2月28日

--

![](../Images/b531bf32d722dc2e92699ee58b49dc3b.png)

大盐湖地区的时间流逝影像，基于2014年至2023年的无云Landsat-8图像，由作者可视化

# 目录

1.  🌅 **大盐湖缩小问题简介**

1.  💾 **下载Landsat-8图像**

1.  📈 **来自统计文件的大盐湖地区时间序列**

1.  ⚙️ **处理Landsat-8图像**

1.  🗺️ **大盐湖图像的可视化**

1.  **🎥 大盐湖缩小的时间流逝影像**

1.  📉 **基于分类图像的大盐湖地区时间序列**

1.  ⚖️ **比较统计文件和图像中的时间序列**

1.  **📄 结论**

1.  **📚 参考文献**

## 🌅 **大盐湖缩小问题简介**

美国犹他州的大盐湖正在经历长期缩小。根据多项报告，与1986年最大水面相比，湖泊已缩小超过30%。关于湖泊水位下降，有几个原因被提及……
