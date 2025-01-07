# 你需要知道的三个重要 Pandas 函数

> 原文：[`towardsdatascience.com/three-important-pandas-functions-you-need-to-know-32325f96e3a4?source=collection_archive---------2-----------------------#2024-12-25`](https://towardsdatascience.com/three-important-pandas-functions-you-need-to-know-32325f96e3a4?source=collection_archive---------2-----------------------#2024-12-25)

## 掌握这些技巧，让你在 Python 开发者中脱颖而出

[](https://medium.com/@jiayanyin.simba?source=post_page---byline--32325f96e3a4--------------------------------)![Jiayan Yin](https://medium.com/@jiayanyin.simba?source=post_page---byline--32325f96e3a4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--32325f96e3a4--------------------------------)![数据科学之路](https://towardsdatascience.com/?source=post_page---byline--32325f96e3a4--------------------------------) [Jiayan Yin](https://medium.com/@jiayanyin.simba?source=post_page---byline--32325f96e3a4--------------------------------)

·发表于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--32325f96e3a4--------------------------------) ·阅读时间 7 分钟·2024 年 12 月 25 日

--

![](img/f00130ffa41ead0c96c90a9949125132.png)

图片来源：[Zach Graves](https://unsplash.com/@zgraves?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你问数据科学家最常用的 Python 库是哪一个，答案无疑是 Pandas。Pandas 用于处理数据集，提供了分析、清理、探索和操作数据的功能。此外，Pandas 还可以用于运行描述性统计分析。使用 Python 进行项目的数据显示科学家从第一天起就会熟悉 Pandas。那么，今天我为什么要讨论 Pandas 呢？

事实上，有一些 Pandas 函数是许多用户容易忽视或没有完全探索的。因此，我将在今天的文章中讨论这些函数。

# 自定义函数 apply() 方法

apply()方法沿着 DataFrame 或 Series 的轴应用自定义函数。***该方法对于需要使用用户定义的函数处理数据并使数据转换更具灵活性的复杂计算非常有用。***例如，如果你想清理一个包含混乱的产品名称和价格的数据集，你需要将产品名称右对齐，使用单词“英寸”而不是符号，添加适当的空格，保持单词的正确大小写，并删除价格栏中的美元符号。你可以通过 apply 方法完成所有这些任务……
