# 如何在 Python 中制作赛博朋克“暗黑模式”数据可视化

> 原文：[`towardsdatascience.com/how-to-make-cyberpunk-dark-mode-data-visualizations-in-python-b28f82386e75?source=collection_archive---------3-----------------------#2024-04-08`](https://towardsdatascience.com/how-to-make-cyberpunk-dark-mode-data-visualizations-in-python-b28f82386e75?source=collection_archive---------3-----------------------#2024-04-08)

## 霓虹线条与暗黑设计，简介

[](https://medium.com/@mia_dwyer?source=post_page---byline--b28f82386e75--------------------------------)![Mia Dwyer](https://medium.com/@mia_dwyer?source=post_page---byline--b28f82386e75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b28f82386e75--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b28f82386e75--------------------------------) [Mia Dwyer](https://medium.com/@mia_dwyer?source=post_page---byline--b28f82386e75--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b28f82386e75--------------------------------) ·阅读时间 4 分钟·2024 年 4 月 8 日

--

我一直喜欢在图表上使用黑色背景和霓虹线条，不仅因为它们的美学效果，还因为它们能改善某些类型视力障碍者的可访问性——在本文中，我们将讨论如何使用 Python 创建一些非常酷且具有美学感的赛博朋克风格图表。

（附注：感谢我的一位早期分析经理，他曾说我的图表丑且不专业——你肯定会讨厌这篇文章！👋）

![](img/b7bfb7fea63d7b19bf45262b25a1cedf.png)

图片由我创作，使用 DALL-E

在 Chime，我和我的朋友 Maia Bittner 曾经在 Google Sheets 和 Excel 中创建过像这样的图表，通过手动选择所有的霓虹色彩来使我们的数据更加引人注目且富有互动性。使用这种方法，你也可以轻松在 Python 中生成这些图表！

现在，让我们看看如何使用 Palmer Penguins 数据集创建一些酷炫的赛博朋克风格霓虹数据可视化。你可以通过安装 `palmerpenguins` 在 Python 笔记本中直接访问该数据集。

> 💫 介绍 mplcyberpunk，一个 *“基于 matplotlib 的 Python 包，通过额外三行代码创建 ‘赛博朋克’ 风格的图表。”*
