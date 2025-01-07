# 使用dbt在Snowflake表格上创建动态数据透视表

> 原文：[https://towardsdatascience.com/creating-dynamic-pivots-on-snowflake-tables-with-dbt-9f61457b0adf?source=collection_archive---------3-----------------------#2024-11-09](https://towardsdatascience.com/creating-dynamic-pivots-on-snowflake-tables-with-dbt-9f61457b0adf?source=collection_archive---------3-----------------------#2024-11-09)

## 利用dbt及其高级脚本功能生成适应于变化数据透视值的动态数据透视表

[](https://medium.com/@broepke?source=post_page---byline--9f61457b0adf--------------------------------)[![Brian Roepke](../Images/0b7ef72cbfc9acda69fde14127d65dcf.png)](https://medium.com/@broepke?source=post_page---byline--9f61457b0adf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9f61457b0adf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9f61457b0adf--------------------------------) [Brian Roepke](https://medium.com/@broepke?source=post_page---byline--9f61457b0adf--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9f61457b0adf--------------------------------) ·阅读时间6分钟·2024年11月9日

--

![](../Images/29f9a6689356cfa53648b58e68f1b501.png)

图片来源：[Possessed Photography](https://unsplash.com/@possessedphotography?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 什么是数据透视表？

在处理表格数据时，通常会将数据以最适合日常操作的格式进行布局。例如，表示不同客户购买交易的行。

数据透视表允许你将行旋转为列，从而为大型数据集创建一个更有组织且易于消化的格式。你还可以对数据进行聚合，例如计算总和、平均值或计数，并将这些结果作为每个汇总类别的独立列进行显示。

让我们来看一下一个有助于描述这一点的图示。在这里，我们按用户值进行分组，然后计算每个交易的计数之和，并为每个产品创建一个独立的列。

![](../Images/8c1fe2260e7376d743d8c0089421a221.png)

图片来源：作者

# 手动方法

我们大多数人会通过编写带有条件语句的SQL来处理每个类别。虽然这种方法适用于简单的情况，但如果…
