# 比较 Pandas 和 (%%SQL) 在 Python 中的数据分析

> 原文：[`towardsdatascience.com/comparing-pandas-and-sql-for-data-analysis-in-python-caf2b99f414b?source=collection_archive---------2-----------------------#2024-10-28`](https://towardsdatascience.com/comparing-pandas-and-sql-for-data-analysis-in-python-caf2b99f414b?source=collection_archive---------2-----------------------#2024-10-28)

## 利用 SQL 和 Pandas 从糖尿病患者记录中提取洞察

[](https://medium.com/@panData?source=post_page---byline--caf2b99f414b--------------------------------)![Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--caf2b99f414b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--caf2b99f414b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--caf2b99f414b--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--caf2b99f414b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--caf2b99f414b--------------------------------) ·阅读时间 30 分钟·2024 年 10 月 28 日

--

![](img/76be343274aeb917103bfe8f2e51af0b.png)

图片由 [Stone Wang](https://unsplash.com/@stonewyq?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们将使用**Pima 印第安人糖尿病数据库**，该数据库包含已被诊断为糖尿病或非糖尿病患者的数据。我们的目标是提取一个聚焦于 50 岁以上患者的样本，添加一个新列，用于将每个个体分类为**正常**（BMI < 30）或**肥胖**（BMI ≥ 30）。

该过程将涉及使用 Python 导入数据，创建**数据库副本**，并使用**SQL 查询**执行转换。一旦这些修改完成，我们将把数据转回 Pandas `DataFrame` 中，并将其保存为**CSV 文件**，以供指定的数据科学家进一步分析。

该数据集是公开可用的，可以在此下载：

[](https://www.kaggle.com/datasets/uciml/pima-indians-diabetes-database?source=post_page-----caf2b99f414b--------------------------------) [## Pima 印第安人糖尿病数据库

### 根据诊断数据预测糖尿病的发生

[www.kaggle.com](https://www.kaggle.com/datasets/uciml/pima-indians-diabetes-database?source=post_page-----caf2b99f414b--------------------------------)

# 概述

我将向你展示一个教程，内容是我们如何提取和分析**数据库中的数据**——这是一个极其有用的技能，作为**数据分析师**你将经常在日常工作中用到。我将教你如何…
