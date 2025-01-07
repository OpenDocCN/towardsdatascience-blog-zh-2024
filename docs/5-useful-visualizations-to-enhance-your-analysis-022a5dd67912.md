# 5 种有用的可视化来增强你的分析

> 原文：[`towardsdatascience.com/5-useful-visualizations-to-enhance-your-analysis-022a5dd67912?source=collection_archive---------4-----------------------#2024-03-24`](https://towardsdatascience.com/5-useful-visualizations-to-enhance-your-analysis-022a5dd67912?source=collection_archive---------4-----------------------#2024-03-24)

## 使用 Python 的统计可视化库 Seaborn 来提升你的分析。

[](https://gustavorsantos.medium.com/?source=post_page---byline--022a5dd67912--------------------------------)![Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--022a5dd67912--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--022a5dd67912--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--022a5dd67912--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--022a5dd67912--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--022a5dd67912--------------------------------) ·7 分钟阅读·2024 年 3 月 24 日

--

![](img/da6cf07e13b039cd7ac4f254d751e0f2.png)

图片来源：[ANIRUDH](https://unsplash.com/@lanirudhreddy?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于 [Unsplash](https://unsplash.com/photos/lighted-number-5-kCes633Hh1M?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

Seaborn 已经存在很长时间了。

我敢打赌，它是最著名且最常用的数据可视化库之一，因为它对初学者友好，使非统计学家也能构建强大的图形，从而帮助提取有统计支持的见解。

我不是统计学家。我对这个主题的兴趣来源于数据科学。为了更好地完成我的工作，我需要学习统计概念。因此，我喜欢能轻松访问直方图、置信区间和线性回归的方式，并且代码量非常少。

Seaborn 的语法非常基础：`sns.type_of_plot(data, x, y)`。使用这个简单的模板，我们可以构建许多不同的可视化，例如 `barplot`、`histplot`、`scatterplot`、`lineplot`、`boxplot` 等。

但这篇文章并不是要讨论这些。它讨论的是其他几种增强型可视化，它们能够在你的分析中带来不同。

让我们看看它们是什么。

# 可视化

要创建这些可视化并与本练习一起编写代码，只需使用 `import seaborn as sns` 导入 seaborn 库。
