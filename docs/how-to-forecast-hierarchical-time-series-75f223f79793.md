# 如何预测层次化时间序列

> 原文：[https://towardsdatascience.com/how-to-forecast-hierarchical-time-series-75f223f79793?source=collection_archive---------6-----------------------#2024-08-20](https://towardsdatascience.com/how-to-forecast-hierarchical-time-series-75f223f79793?source=collection_archive---------6-----------------------#2024-08-20)

## 初学者的销售预测对账指南

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--75f223f79793--------------------------------)[![Dr. Robert Kübler](../Images/3b8d8b88f76c0c43d9c305e3885e7ab9.png)](https://dr-robert-kuebler.medium.com/?source=post_page---byline--75f223f79793--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75f223f79793--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--75f223f79793--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--75f223f79793--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75f223f79793--------------------------------) ·阅读时间12分钟·2024年8月20日

--

![](../Images/53a86f51e6fd08473124f09e8616ab8a.png)

图片由[Krista Mangulsone](https://unsplash.com/@krista?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

想象一下，你是一家迷人的小宠物店的数据科学家，这家店专注于五种产品：两种猫粮和三种狗粮。你的任务是什么？通过准确预测每种产品的每周销售量，帮助这家小生意蓬勃发展。目标是提供一份全面的销售预测——包括总销售量，以及猫粮和狗粮的详细预测，甚至是每种产品的销售预测。

## 数据

你拥有不同种类的猫粮A和B，以及不同种类的狗粮C、D和E在200天内的销售数据。幸运的是，这些数据非常**干净**，**没有缺失值**，也**没有异常值**。此外，数据中**没有趋势**。看起来是这样的：

![](../Images/2719f02effe659290fba3e913dcd9cca.png)

图片来自作者。

> ***注意：***我自己生成了这些数据。

除了单独的销售数据，我们还有所有猫粮产品、所有狗粮产品和所有产品的汇总销售数据。我们将这样的时间序列集合称为**层次化时间序列**。在我们的案例中，它们**遵循以下销售层级结构**：
