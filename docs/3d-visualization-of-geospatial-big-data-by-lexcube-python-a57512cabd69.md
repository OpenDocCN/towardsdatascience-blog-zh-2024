# Lexcube 的地理空间大数据三维可视化！（Python）

> 原文：[`towardsdatascience.com/3d-visualization-of-geospatial-big-data-by-lexcube-python-a57512cabd69?source=collection_archive---------5-----------------------#2024-02-12`](https://towardsdatascience.com/3d-visualization-of-geospatial-big-data-by-lexcube-python-a57512cabd69?source=collection_archive---------5-----------------------#2024-02-12)

## 学习如何使用 Lexcube，这个用于时空域数据可视化的 Python 包！

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--a57512cabd69--------------------------------)![Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--a57512cabd69--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a57512cabd69--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a57512cabd69--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--a57512cabd69--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a57512cabd69--------------------------------) ·阅读时间 9 分钟·2024 年 2 月 12 日

--

# 目录

1.  **🌟 介绍**

1.  🌐 **Lexcube**

1.  📰 **数据**

1.  📂 **带有随机数的数据立方体**

1.  🗂️ **带有气候数据的数据立方体**

1.  **🔄 光栅图层转为 Xarray**

1.  🌍 **Lexcube 的 Xarray 三维可视化**

1.  **📦 我们还能用 Lexcube 做什么**

1.  **📝 结论**

1.  **📚 参考文献**

## **🌟 介绍**

三维数据可视化（纬度、经度和时间）非常迷人，对吧？作为一名地理空间数据科学家，我一直想知道如何以最简单的方式绘制一个由数百个光栅图层合并而成的立方体数据集。在我浏览 LinkedIn 信息流时，我发现了一个很棒的 Python 库，叫做 Lexcube，它最近已在 Jupyter Notebook 上发布。关于 Lexcube 的更多信息，请参考这篇[***文章***](https://www.computer.org/csdl/magazine/cg/2024/01/10274107/1R6MgauvfWg)或查看[***Lexcube 在 GitHub 上的页面***](https://github.com/msoechting/lexcube)***。***

首先，我要感谢 Miguel Mahecha 在 LinkedIn 上分享这篇文章，还要感谢 Maximilian Söchting 和他的团队开发了一个对地理空间数据社区非常有价值的工具。其次，这里有一个实际操作练习，帮助你使用这个包…
