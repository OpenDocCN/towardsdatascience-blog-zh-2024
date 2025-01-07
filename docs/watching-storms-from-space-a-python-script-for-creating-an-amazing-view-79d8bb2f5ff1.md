# 从太空观察风暴：一个用于创建惊人视图的 Python 脚本

> 原文：[`towardsdatascience.com/watching-storms-from-space-a-python-script-for-creating-an-amazing-view-79d8bb2f5ff1?source=collection_archive---------4-----------------------#2024-02-06`](https://towardsdatascience.com/watching-storms-from-space-a-python-script-for-creating-an-amazing-view-79d8bb2f5ff1?source=collection_archive---------4-----------------------#2024-02-06)

## 使用 Python 和 Google Colab 从静止卫星图像创建动画，实时动态监控风暴（大气河流）

[](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--79d8bb2f5ff1--------------------------------)![Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--79d8bb2f5ff1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--79d8bb2f5ff1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--79d8bb2f5ff1--------------------------------) [Mahyar Aboutalebi, Ph.D. 🎓](https://medium.com/@mahyar.aboutalebi?source=post_page---byline--79d8bb2f5ff1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--79d8bb2f5ff1--------------------------------) ·13 分钟阅读·2024 年 2 月 6 日

--

# 目录

1.  **🌟 介绍**

1.  🌐 **静止轨道气象卫星（GOES）**

1.  **🔍 AWS 中的 GOES 数据库**

1.  ⏳ **下载 GOES NetCDF 文件**

1.  📊 **GOES 图像的可视化**

1.  **🎥 风暴的时间推移**

1.  **📄 结论**

1.  **📚 参考文献**

## **🌟 介绍**

如果你在关注新闻，你会知道目前有一个突发新闻，关于即将袭击加利福尼亚并影响中南部地区的强大风暴，称为大气河流。大气河流是一个狭窄的风带，能够从热带地区输送浓缩的水蒸气（湿气）。预计即将来临的风暴将给该州部分地区带来强降雨和严重的突发洪水。

昨晚，在查看我的 Twitter 账户（现在叫 X）时，我看到许多关于风暴的推文，以及一些色彩斑斓的地图，预测并模拟了加利福尼亚州的降水量。这些地图很快成为了我撰写这篇文章的动力。
