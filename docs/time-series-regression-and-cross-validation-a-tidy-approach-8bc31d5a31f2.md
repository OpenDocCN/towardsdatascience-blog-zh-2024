# 时间序列回归与交叉验证：一种简洁的方法

> 原文：[`towardsdatascience.com/time-series-regression-and-cross-validation-a-tidy-approach-8bc31d5a31f2?source=collection_archive---------6-----------------------#2024-06-12`](https://towardsdatascience.com/time-series-regression-and-cross-validation-a-tidy-approach-8bc31d5a31f2?source=collection_archive---------6-----------------------#2024-06-12)

## 使用 tidymodels、modeltime 和 timetk 进行 EDA、特征工程、交叉验证和模型比较的逐步指南

[](https://medium.com/@menghani.deepsha?source=post_page---byline--8bc31d5a31f2--------------------------------)![Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--8bc31d5a31f2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8bc31d5a31f2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8bc31d5a31f2--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--8bc31d5a31f2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8bc31d5a31f2--------------------------------) ·阅读时间：8 分钟·2024 年 6 月 12 日

--

![](img/703835eb4bbd516d5cc3bd61372abd8a.png)

图片由[Aron Visuals](https://unsplash.com/@aronvisuals?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/selective-focus-photo-of-brown-and-blue-hourglass-on-stones-BXOXnQ26B7o?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

时间序列预测方法一直在不断发展，虽然 ARIMA 一直是基础模型，但机器学习模型也展现了巨大的潜力。各行业中有许多场景可以从更加准确的时间数据建模中获益。在本文中，我将处理一个这样的场景——销售预测。让我们直接开始吧，节省时间（明白了吗？）。

# 代码

本文中重现所有内容的代码可以在我的[GitHub 仓库](https://github.com/deepshamenghani/time-series-regression-tidymodels)找到。

# 数据集

对于本次练习，我使用了由[Samuel Cortinhas 提供的时间序列练习数据集](https://www.kaggle.com/datasets/samuelcortinhas/time-series-practice-dataset)，该数据集在 Kaggle 上以[CC0：公共领域](https://creativecommons.org/publicdomain/zero/1.0/)许可发布。此数据集包含模拟的时间序列数据，覆盖了 10 年（2010–2019），并包括日期、商店 ID、产品 ID 和销售额等特征。为了进行此次分析，我选择了一个商店和一个产品，以便集中关注回归分析的各个组件。

# 时间序列分析

## 步骤 0：设置
