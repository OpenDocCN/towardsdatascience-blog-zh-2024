# 循环编码：时间序列特征的替代方法

> 原文：[https://towardsdatascience.com/cyclical-encoding-an-alternative-to-one-hot-encoding-for-time-series-features-4db46248ebba?source=collection_archive---------3-----------------------#2024-05-03](https://towardsdatascience.com/cyclical-encoding-an-alternative-to-one-hot-encoding-for-time-series-features-4db46248ebba?source=collection_archive---------3-----------------------#2024-05-03)

## 循环编码为你的模型提供了相同的信息，但使用了显著更少的特征。

[](https://medium.com/@pelletierhaden?source=post_page---byline--4db46248ebba--------------------------------)[![Haden Pelletier](../Images/8f73fc8222e783883c4ebcaee14513e0.png)](https://medium.com/@pelletierhaden?source=post_page---byline--4db46248ebba--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4db46248ebba--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4db46248ebba--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--4db46248ebba--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4db46248ebba--------------------------------) ·阅读时长 7 分钟·2024年5月3日

--

在训练时间序列的机器学习模型时，通常你需要使用以下时间特征：

+   小时

+   一周中的天数

+   月份

+   一年中的周数或天数

+   等等。

将时间戳列转换为这些特征是相当容易的。在确保你已经将时间列转换为日期时间对象（使用 `pd.to_datetime`）后，你可以通过 `.dt` 提取一系列时间序列特征。

```py
df['Hour']=df['Datetime'].dt.hour
df['Month']=df['Datetime'].dt.month
df['Dayofweek']=df['Datetime'].dt.dayofweek
```

作为参考，我将在这个示例中使用的[数据集](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption?resource=download)（[CC0 公共领域许可](https://creativecommons.org/publicdomain/zero/1.0/)）是一个小时电力消耗数据集。能源消耗数据集通常是时间序列数据，目标最终是通过过去的数据预测未来的消耗量，因此这是一个很好的应用案例。尽管其他外部特征，如温度、湿度和风速也会影响能源消耗，但在这里我将专注于提取和转换时间序列特征。
