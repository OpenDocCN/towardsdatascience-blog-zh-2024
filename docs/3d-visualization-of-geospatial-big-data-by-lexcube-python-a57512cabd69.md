# Lexcube的地理空间大数据三维可视化！（Python）

> 原文：[https://towardsdatascience.com/3d-visualization-of-geospatial-big-data-by-lexcube-python-a57512cabd69?source=collection_archive---------5-----------------------#2024-02-12](https://towardsdatascience.com/3d-visualization-of-geospatial-big-data-by-lexcube-python-a57512cabd69?source=collection_archive---------5-----------------------#2024-02-12)

## 学习如何使用Lexcube，这个用于时空域数据可视化的Python包！

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--a57512cabd69--------------------------------)[![Mahyar Aboutalebi, Ph.D. 🎓](../Images/83d62352800f8a2932db8a07997c8059.png)](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--a57512cabd69--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a57512cabd69--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a57512cabd69--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--a57512cabd69--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a57512cabd69--------------------------------) ·阅读时间 9 分钟·2024年2月12日

--

# 目录

1.  **🌟 介绍**

1.  🌐 **Lexcube**

1.  📰 **数据**

1.  📂 **带有随机数的数据立方体**

1.  🗂️ **带有气候数据的数据立方体**

1.  **🔄 光栅图层转为Xarray**

1.  🌍 **Lexcube的Xarray三维可视化**

1.  **📦 我们还能用Lexcube做什么**

1.  **📝 结论**

1.  **📚 参考文献**

## **🌟 介绍**

三维数据可视化（纬度、经度和时间）非常迷人，对吧？作为一名地理空间数据科学家，我一直想知道如何以最简单的方式绘制一个由数百个光栅图层合并而成的立方体数据集。在我浏览LinkedIn信息流时，我发现了一个很棒的Python库，叫做Lexcube，它最近已在Jupyter Notebook上发布。关于Lexcube的更多信息，请参考这篇[***文章***](https://www.computer.org/csdl/magazine/cg/2024/01/10274107/1R6MgauvfWg)或查看[***Lexcube在GitHub上的页面***](https://github.com/msoechting/lexcube)***。***

首先，我要感谢Miguel Mahecha在LinkedIn上分享这篇文章，还要感谢Maximilian Söchting和他的团队开发了一个对地理空间数据社区非常有价值的工具。其次，这里有一个实际操作练习，帮助你使用这个包…
