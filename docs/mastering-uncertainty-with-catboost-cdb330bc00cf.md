# 精通 CatBoost 的不确定性

> 原文：[`towardsdatascience.com/mastering-uncertainty-with-catboost-cdb330bc00cf?source=collection_archive---------5-----------------------#2024-03-29`](https://towardsdatascience.com/mastering-uncertainty-with-catboost-cdb330bc00cf?source=collection_archive---------5-----------------------#2024-03-29)

## 揭示预测区间的力量

[](https://medium.com/@ilia.teimouri?source=post_page---byline--cdb330bc00cf--------------------------------)![Ilia Teimouri 博士](https://medium.com/@ilia.teimouri?source=post_page---byline--cdb330bc00cf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cdb330bc00cf--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cdb330bc00cf--------------------------------) [Ilia Teimouri 博士](https://medium.com/@ilia.teimouri?source=post_page---byline--cdb330bc00cf--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cdb330bc00cf--------------------------------) ·6 分钟阅读·2024 年 3 月 29 日

--

![](img/60c99d8fbc4f7bde1f6721d3727d574b.png)

照片由[Ian Taylor](https://unsplash.com/@carrier_lost)提供，来源于[Unsplash](http://unsplash.com)。

预测区间在回归分析中起着至关重要的作用，尤其是在目标不仅仅是点预测，而是评估预测的不确定性或变动性时。与提供每个输入单一估计值的点预测不同，预测区间提供了一个范围，真实值预计会以一定的概率落在该范围内。这一点尤为重要，因为它考虑到了任何预测模型中的固有不确定性。通过量化这种不确定性，预测区间提供了对可能结果的更全面理解。例如，在金融预测中，了解未来回报可能波动的范围对于风险管理和投资策略至关重要。

此外，在回归模型中，追求创建最窄或最“高效”的预测区间可以提高模型输出的精度和可靠性。较窄的区间表示预测的确定性更高，前提是这些区间准确并持续捕捉真实值。

通常，预测区间的写法为：

[𝜇-𝘻𝜎, 𝜇+𝘻𝜎]
