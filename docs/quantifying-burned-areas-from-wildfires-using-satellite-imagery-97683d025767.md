# 使用卫星影像量化野火的烧毁区域

> 原文：[`towardsdatascience.com/quantifying-burned-areas-from-wildfires-using-satellite-imagery-97683d025767?source=collection_archive---------7-----------------------#2024-07-22`](https://towardsdatascience.com/quantifying-burned-areas-from-wildfires-using-satellite-imagery-97683d025767?source=collection_archive---------7-----------------------#2024-07-22)

## 使用 Google Colab 中的 Python 和 Sentinel-2 图像来确定由于野火造成的森林烧毁面积

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--97683d025767--------------------------------)![Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--97683d025767--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--97683d025767--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--97683d025767--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--97683d025767--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--97683d025767--------------------------------) ·阅读时间 9 分钟·2024 年 7 月 22 日

--

![](img/dbb9a62f379d82bc1f6f7a89e3a47dc5.png)

由作者可视化的 Walbridge 火灾事件期间捕获的不同波段的 Sentinel-2 图像

# 目录

1.  **🌟 介绍**

1.  🔥 **加利福尼亚 Walbridge 野火**

1.  **🏷️ 方法**

1.  🚀 **设置 Google Colab**

1.  🛰️ **加载清晰的 Sentinel-2 图像**

1.  🔢 **计算植被像素数量**

1.  📈 **绘制植被面积的时间序列图**

1.  🌍 **野火的 Sentinel-2 图像可视化**

1.  **📄 结论**

1.  **📚 参考文献**

## **🌟 介绍**

几年前，当大多数气候模型预测如果我们不采取必要措施，更多的洪水、热浪和野火将发生时，我并没有预料到这些异常灾难性现象会成为常见事件。在这些灾难中，野火每年摧毁大量的森林面积。如果你搜索不同地方的重大野火数据表，你会发现令人震惊的统计数字，显示由于野火，地球上有多少森林面积正在消失。
