# 使用 XGBoost 进行交叉验证——通过 Tidymodels 增强客户流失分类

> 原文：[`towardsdatascience.com/cross-validation-with-xgboost-enhancing-customer-churn-classification-with-tidymodels-fd9e1bc8696d?source=collection_archive---------6-----------------------#2024-06-06`](https://towardsdatascience.com/cross-validation-with-xgboost-enhancing-customer-churn-classification-with-tidymodels-fd9e1bc8696d?source=collection_archive---------6-----------------------#2024-06-06)

## 使用 XGBoost 和 Tidymodels 实施交叉验证、特征工程和模型评估的逐步指南

[](https://medium.com/@menghani.deepsha?source=post_page---byline--fd9e1bc8696d--------------------------------)![Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--fd9e1bc8696d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fd9e1bc8696d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fd9e1bc8696d--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--fd9e1bc8696d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fd9e1bc8696d--------------------------------) ·阅读时间：6 分钟·2024 年 6 月 6 日

--

![](img/e195df24f869213bc14b5df8dff82ba6.png)

图片由[Kevin Ku](https://unsplash.com/@ikukevk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/closeup-photo-of-eyeglasses-w7ZyuGYNpRQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在我之前的文章中，我展示了如何使用逻辑回归和随机森林构建客户流失预测模型。在本文中，我想展示如何通过交叉验证，确保模型的表现不依赖于单一的训练/测试集划分，从而使建模过程更加稳健。

# 什么是交叉验证？

交叉验证通过将数据划分为训练集和测试集来实现，模型在不同的子集上进行拟合。其目的是评估模型的表现，并确保其在未见过的数据上的泛化能力。这还可以帮助防止过拟合，尤其是在模型在训练集上的表现远优于测试集时，从而提高模型的鲁棒性。

由于我在上一篇文章中介绍了逻辑回归和随机森林，这篇文章将展示如何使用相同的数据集进行 XGBoost 的交叉验证——[银行流失数据的二分类](https://www.kaggle.com/datasets/cybersimar08/binary-classification-of-bank-churn-synthetic-data)，由 Simarpreet Singh 提供，并根据[创作共用署名 4.0](https://creativecommons.org/licenses/by/4.0/)协议进行授权…
