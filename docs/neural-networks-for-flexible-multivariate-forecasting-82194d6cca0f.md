# 神经网络用于灵活的多变量预测

> 原文：[https://towardsdatascience.com/neural-networks-for-flexible-multivariate-forecasting-82194d6cca0f?source=collection_archive---------5-----------------------#2024-10-25](https://towardsdatascience.com/neural-networks-for-flexible-multivariate-forecasting-82194d6cca0f?source=collection_archive---------5-----------------------#2024-10-25)

## 一步步轻松入门神经网络进行时间序列预测

[](https://medium.com/@seelcs12?source=post_page---byline--82194d6cca0f--------------------------------)[![Lucas See](../Images/ae99e98e11cae99708e5abd681edd7a3.png)](https://medium.com/@seelcs12?source=post_page---byline--82194d6cca0f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--82194d6cca0f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--82194d6cca0f--------------------------------) [Lucas See](https://medium.com/@seelcs12?source=post_page---byline--82194d6cca0f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82194d6cca0f--------------------------------) ·14分钟阅读·2024年10月25日

--

![](../Images/49dec5d9526818359ebee72fa063a7ea.png)

图片由[Aron Visuals](https://unsplash.com/@aronvisuals?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

预测多个时间序列可能会迅速变得复杂；传统方法要么需要为每个序列单独建立模型（例如SARIMA），要么要求所有序列必须相关（例如VARMA）。神经网络提供了一种灵活的方法，能够使用单一模型进行多系列预测，无论这些系列是否相关。

此外，这种方法允许轻松地将外生变量纳入其中，并可以预测未来多个时间步的值，从而提供一个强大的通用解决方案，能够在各种情况下表现良好。

在本文中，我们将展示如何执行数据窗口化操作，将我们的数据从时间序列转化为监督学习格式，适用于单变量和多变量时间序列。一旦数据转换完成，我们将展示如何训练深度神经网络和LSTM来进行多变量预测。

**检查我们的数据**

我们将使用一个数据集，该数据集记录了2013年至2016年间印度德里的每日平均温度和湿度。该数据可在Kaggle上获取，并且根据[CC0: 公共领域](https://creativecommons.org/publicdomain/zero/1.0/)许可证进行使用，非常适合…
