# 话题建模你的个人数据

> 原文：[https://towardsdatascience.com/topic-modelling-your-personal-data-9561e25a042e?source=collection_archive---------6-----------------------#2024-09-21](https://towardsdatascience.com/topic-modelling-your-personal-data-9561e25a042e?source=collection_archive---------6-----------------------#2024-09-21)

## 使用传统模型和变换器模型探索由数据经纪人存储的个人数据

[](https://medium.com/@jbraun_44616?source=post_page---byline--9561e25a042e--------------------------------)[![Jeff Braun](../Images/c630fa32dde9d861ab9b2dcf11bff1e3.png)](https://medium.com/@jbraun_44616?source=post_page---byline--9561e25a042e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9561e25a042e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9561e25a042e--------------------------------) [Jeff Braun](https://medium.com/@jbraun_44616?source=post_page---byline--9561e25a042e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9561e25a042e--------------------------------) ·阅读时长 24 分钟·2024年9月21日

--

![](../Images/4a9ad3270d2311c09eb1539f2af8e7d0.png)

图像由作者使用 ChatGPT 4o 和 DALL-E-3 创建

在之前的[文章](https://medium.com/towards-data-science/accessing-your-personal-data-569b8991d745)中，我描述了如何访问存储并被我们每天接触的前线消费者公司使用的个人数据。这些公司包括零售商、社交媒体、电信提供商、金融服务公司等。我探讨了如何利用各种机器学习模型和可视化工具，发现这些公司如何看待我们。

在撰写那篇文章的过程中，我发现前线公司经常将我们的个人数据与另一类公司共享，这些公司通常被称为数据经纪人或数据聚合商（以下简称聚合商）。聚合商通过其他类型的数据增强我们的数据，这些数据来源于公共记录、其他聚合商以及类似来源，从而创建我们的个人档案。然后，他们将这些档案出售给面向消费者的公司和其他组织，用于营销或其他目的。

我的好奇心被激发了：这些聚合商到底存储了哪些类型的数据？他们存储了多少特征？是否有一些主要类型的数据是个别聚合商重点关注的？如果有的话，这能告诉我他们的最终客户是什么样的？这些最终客户属于哪些行业，他们需要哪些个人数据…
