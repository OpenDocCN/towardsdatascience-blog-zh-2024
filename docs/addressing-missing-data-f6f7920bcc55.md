# 处理缺失数据

> 原文：[https://towardsdatascience.com/addressing-missing-data-f6f7920bcc55?source=collection_archive---------9-----------------------#2024-11-26](https://towardsdatascience.com/addressing-missing-data-f6f7920bcc55?source=collection_archive---------9-----------------------#2024-11-26)

## 了解缺失数据模式（MCAR、MNAR、MAR），以利用Missingno提高模型性能

[](https://gizkaya.medium.com/?source=post_page---byline--f6f7920bcc55--------------------------------)[![Gizem Kaya](../Images/29d29f04df742f8c19cb9fb0c7169e5d.png)](https://gizkaya.medium.com/?source=post_page---byline--f6f7920bcc55--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f6f7920bcc55--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f6f7920bcc55--------------------------------) [Gizem Kaya](https://gizkaya.medium.com/?source=post_page---byline--f6f7920bcc55--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6f7920bcc55--------------------------------) ·8分钟阅读·2024年11月26日

--

在理想的世界中，我们希望能使用干净、完整且准确的数据集。然而，现实世界中的数据很少符合我们的期望。我们常常遇到包含噪声、不一致性、离群值和缺失数据的数据集，这需要仔细处理才能得到有效的结果。特别是，缺失数据是一个不可避免的挑战，我们如何处理它对预测模型或分析结果有着重要影响。

为什么？

其原因隐藏在定义中。**缺失数据**是那些**如果被观察到**将对分析具有意义的未观察值。

![](../Images/6fee397a7a886189e10f9318c4e78e5b.png)

摄影：由[Tanja Tepavac](https://unsplash.com/@ttepavac?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在文献中，我们可以找到几种处理缺失数据的方法，但根据缺失的性质，选择合适的技术至关重要。像删除缺失值行这样简单的方法可能会导致偏差或丢失重要的信息。错误的填充值也可能导致扭曲，影响最终结果。因此，在决定修正措施之前，理解数据中缺失性的性质至关重要。

缺失性的性质可以简单地分为三种：
