# 客户细分项目：训练、测试、调整、重复

> 原文：[https://towardsdatascience.com/customer-segmentation-cluster-project-train-test-tweak-repeat-2bc93874b315?source=collection_archive---------5-----------------------#2024-08-23](https://towardsdatascience.com/customer-segmentation-cluster-project-train-test-tweak-repeat-2bc93874b315?source=collection_archive---------5-----------------------#2024-08-23)

## 使用K-Means聚类学习客户细分。

[](https://medium.com/@panData?source=post_page---byline--2bc93874b315--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--2bc93874b315--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2bc93874b315--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2bc93874b315--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--2bc93874b315--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2bc93874b315--------------------------------) ·阅读时间33分钟·2024年8月23日

--

![](../Images/e1efe48276717212b1802beb2d847a11.png)

图片由[nrd](https://unsplash.com/@nicotitto?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我现在为你带来一个**数据科学在营销中的应用——客户基础细分**（聚类）项目。这个项目是一个高层次的项目，可以应用于任何需要理解客户差异的公司。**知道公司迫切想要** 👹 **了解他们的客户是谁。** 你认为所有客户都一样吗？

不，客户并不都一样。他们有不同的口味、不同的偏好，而且他们处在人生的不同阶段，在这些阶段，某些产品特性可能会有更大或更小的价值。

但是，**首先，我诚恳地请求你**，如果你能👏支持这个项目，对我帮助非常大，真的。**我重申**，这项我系统化展示和解释的工作，是为了那些想要学习的人；如果只是为了我自己……好吧，那不过是乱写一通。无论如何，我依赖你的支持。接着说……

# **细分目的**

细分将具有相似特征的客户分为一组。例如，在考虑同一产品时：

+   某个客户可能因为其当前的生活阶段，而更看重价格。

+   另一个可能更看重质量而非成本，理解……
