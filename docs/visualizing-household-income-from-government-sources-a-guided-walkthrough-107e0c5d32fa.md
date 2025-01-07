# 从政府来源可视化家庭收入 —— 引导式操作指南

> 原文：[`towardsdatascience.com/visualizing-household-income-from-government-sources-a-guided-walkthrough-107e0c5d32fa?source=collection_archive---------8-----------------------#2024-03-07`](https://towardsdatascience.com/visualizing-household-income-from-government-sources-a-guided-walkthrough-107e0c5d32fa?source=collection_archive---------8-----------------------#2024-03-07)

## 使用可以应用于你自己数据的 Python 代码，进行特定的可视化操作

[](https://medium.com/@byjameskoh?source=post_page---byline--107e0c5d32fa--------------------------------)![James Koh, PhD](https://medium.com/@byjameskoh?source=post_page---byline--107e0c5d32fa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--107e0c5d32fa--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--107e0c5d32fa--------------------------------) [James Koh, PhD](https://medium.com/@byjameskoh?source=post_page---byline--107e0c5d32fa--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--107e0c5d32fa--------------------------------) ·12 分钟阅读·2024 年 3 月 7 日

--

![](img/e917ed6a4f3362dd7dc2ba9848b11777.png)

该图像由 DALL·E 3 基于“绘制一张代表家庭收入的图像。图像应包括一座房子、一只手上的现金堆和一些统计图表”生成。

你是否不确定如何在互联网上的大量数据中导航，以及在从可靠来源获取相关数据后可以做些什么？

你是否好奇**你的**家庭收入与全国其他家庭相比如何？（*我猜无论职业或兴趣如何，至少 90%的人都有这个疑问。*)

如果你对上述任一问题的回答是“是”，那么这篇文章就是为你准备的！

在这篇引导式的操作指南结束时，你将能够自己获得以下内容。只需根据你的家庭收入，替换下面代码中的*单一变量*（如 `### just replace this with your data ###` 所示），你将能够创建一个显示你相对于新加坡家庭的百分位的图表！（通过额外的工作，你还可以使用你自己国家的特定数据替换数据框。）

![](img/2548244fecd3ebc9435c600b61c4d6c6.png)

2000 年至 2023 年间的月度家庭收入的累计分布函数（y 轴），（x 轴）表示收入，其中标记显示 2000 年收入为 2500 美元的家庭的收入和百分位，收入每年增加 500 美元，直到 2023 年达到 14000 美元。截图由作者提供。
