# 客户流失：如何定义客户未告知其离开的流失

> 原文：[`towardsdatascience.com/customer-attrition-how-to-define-churn-when-customers-do-not-tell-theyre-leaving-ddde83e24e8e?source=collection_archive---------3-----------------------#2024-02-15`](https://towardsdatascience.com/customer-attrition-how-to-define-churn-when-customers-do-not-tell-theyre-leaving-ddde83e24e8e?source=collection_archive---------3-----------------------#2024-02-15)

## 作者详细说明了在某些情况下如何定义客户流失，这些情况下流失并不明显：零售和银行业。

[](https://medium.com/@guillaume.colley?source=post_page---byline--ddde83e24e8e--------------------------------)![Guillaume Colley](https://medium.com/@guillaume.colley?source=post_page---byline--ddde83e24e8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ddde83e24e8e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ddde83e24e8e--------------------------------) [Guillaume Colley](https://medium.com/@guillaume.colley?source=post_page---byline--ddde83e24e8e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ddde83e24e8e--------------------------------) ·阅读时长 9 分钟·2024 年 2 月 15 日

--

![](img/ed2fa715cd2d0de3a3dcb9ea09dae277.png)

图片来源：[Mantas Hesthaven](https://unsplash.com/@mantashesthaven?utm_source=medium&utm_medium=referral) 由[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)提供

# 介绍

留住客户比获取新客户更容易且成本更低（[查看这里](https://www.forbes.com/sites/forbesbusinesscouncil/2022/12/12/customer-retention-versus-customer-acquisition/?sh=4f6d276f1c7d) 和 [查看这里](https://www.linkedin.com/pulse/what-cost-customer-acquisition-vs-retention-ian-kingwill/))。话虽如此，客户保持策略并非免费，最有效的策略仍然可能需要花费不少。由此可见，企业必须识别出最容易流失的客户，并集中精力在这个特定的客户群体上。这就是著名的流失模型，或称流失预测模型发挥作用的地方。它通常是数据科学团队在营销背景下首个需要构建的模型之一。

互联网和电话服务提供商、视频和音频流媒体网站、实体和在线新闻出版物、剃须俱乐部……这些都是客户明确告知公司他们要离开它们的例子——客户通过**取消订阅**或会员资格来告知公司他们的离开。

然而，在许多其他情况下，行业面临着如何定义流失的问题，尤其是当客户**悄然脱离**服务或产品时。很难判断客户何时已流失…
