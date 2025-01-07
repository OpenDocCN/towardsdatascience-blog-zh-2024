# 8 张图表，帮助你向外行解释线性回归

> 原文：[`towardsdatascience.com/8-plots-for-explaining-linear-regression-to-a-layman-489b753da696?source=collection_archive---------2-----------------------#2024-04-16`](https://towardsdatascience.com/8-plots-for-explaining-linear-regression-to-a-layman-489b753da696?source=collection_archive---------2-----------------------#2024-04-16)

## 用残差图、权重图、效应图和 SHAP 图向非技术观众解释回归

[](https://conorosullyds.medium.com/?source=post_page---byline--489b753da696--------------------------------)![Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--489b753da696--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--489b753da696--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--489b753da696--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--489b753da696--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--489b753da696--------------------------------) ·阅读时长：13 分钟·2024 年 4 月 16 日

--

![](img/a8c98d7f3577a9305bf7bb5f32d33442.png)

（来源：[flaticon](https://www.flaticon.com/free-icon/linear-regression_2103640?term=linear+regression&page=1&position=11&origin=search&related_id=2103640) 在[高级计划](https://support.flaticon.com/hc/en-us/articles/202798201-What-are-Flaticon-Premium-licenses-)下）

“不要使用任何数学公式。”这是我经理的指示。

我还能怎么解释回归是如何工作的！？

我当时并不知道，数据科学家工作的很大一部分是要用非技术性语言来解释模型。我们理解一个线性模型所需的，只是一份回归总结。然而，这些数字表格很快就会让（或吓到）其他观众感到厌烦。为了让你的观点更易理解，你需要使用更易消化的可视化工具。所以，我们将探索 8 种图表，帮助你让线性回归变得更易于理解：

1.  残差图

1.  相关性热图

1.  权重图

1.  效应图

1.  平均效应图

1.  个体效应图

1.  趋势效应图

1.  线性模型的 SHAP 值

我们将看到，第 4 到第 7 张图特别有用，因为它们有助于从模型对预测的贡献来解释模型。我们以每个人都能理解的数量来“重新框架”结果。例如，我们可以从解释回归参数转向……
