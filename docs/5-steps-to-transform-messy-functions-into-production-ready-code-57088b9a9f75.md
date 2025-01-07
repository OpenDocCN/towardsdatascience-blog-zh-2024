# 将混乱的函数转换为可生产的代码的5个步骤

> 原文：[https://towardsdatascience.com/5-steps-to-transform-messy-functions-into-production-ready-code-57088b9a9f75?source=collection_archive---------2-----------------------#2024-01-24](https://towardsdatascience.com/5-steps-to-transform-messy-functions-into-production-ready-code-57088b9a9f75?source=collection_archive---------2-----------------------#2024-01-24)

## 数据科学家可扩展且可维护功能指南

[](https://khuyentran1476.medium.com/?source=post_page---byline--57088b9a9f75--------------------------------)[![Khuyen Tran](../Images/98aa66025ad29b618e875c75f1c400a5.png)](https://khuyentran1476.medium.com/?source=post_page---byline--57088b9a9f75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--57088b9a9f75--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--57088b9a9f75--------------------------------) [Khuyen Tran](https://khuyentran1476.medium.com/?source=post_page---byline--57088b9a9f75--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--57088b9a9f75--------------------------------) ·阅读时长11分钟·2024年1月24日

--

![](../Images/cf817a317ba8fcf65d878c3222a5e40d.png)

图片来源：作者

# 动机

函数在数据科学项目中至关重要，因为它们使代码更加模块化、可重用、可读且可测试。然而，编写一个杂乱无章的函数，试图做太多事情，可能会引入维护难题，并降低代码的可读性。

在以下代码中，函数`impute_missing_values`非常长且混乱，试图完成很多事情。由于有许多硬编码的值，其他人将无法在具有不同列名的DataFrame上重用此函数。

```py
def impute_missing_values(df):
    # Fill missing values with group statistics
    df["MSZoning"] = df.groupby("MSSubClass")["MSZoning"].transform(
        lambda x: x.fillna(x.mode()[0])
    )
    df["LotFrontage"] = df.groupby("Neighborhood")["LotFrontage"].transform(
        lambda x: x.fillna(x.median())
    )

    # Fill missing values with constant
    df["Functional"] = df["Functional"].fillna("Typ")

    df["Alley"] = df["Alley"].fillna("Missing")
    for col in ["GarageType", "GarageFinish", "GarageQual", "GarageCond"]:
        df[col] = df[col].fillna("Missing")

    for col in ("BsmtQual", "BsmtCond", "BsmtExposure"…
```
