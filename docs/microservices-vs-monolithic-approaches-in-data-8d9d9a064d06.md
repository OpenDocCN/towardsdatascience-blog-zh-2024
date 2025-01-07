# 数据中的微服务与单体架构

> 原文：[`towardsdatascience.com/microservices-vs-monolithic-approaches-in-data-8d9d9a064d06?source=collection_archive---------1-----------------------#2024-02-16`](https://towardsdatascience.com/microservices-vs-monolithic-approaches-in-data-8d9d9a064d06?source=collection_archive---------1-----------------------#2024-02-16)

![](img/9a4a57b3c96ffb8b964a90da962c7d60.png)

这些令人惊叹的雕像中，唯一比它们更庞大的就是 Airflow。照片由[Stephanie Morcinek](https://unsplash.com/@stephaniemorcinek?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/brown-rock-formation-on-green-grass-field-during-daytime-exBntICqTrs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 微服务与单体架构的争论在软件领域激烈进行，但在数据领域却逐渐平息。

[](https://medium.com/@hugolu87?source=post_page---byline--8d9d9a064d06--------------------------------)![Hugo Lu](https://medium.com/@hugolu87?source=post_page---byline--8d9d9a064d06--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8d9d9a064d06--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d9d9a064d06--------------------------------) [Hugo Lu](https://medium.com/@hugolu87?source=post_page---byline--8d9d9a064d06--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d9d9a064d06--------------------------------) ·阅读时间：8 分钟·2024 年 2 月 16 日

--

# 引言

我不需要花费太多的文字来说服你，选择数据领域中的工具是多么困难。这里有成百上千种方法来[解决这个问题](https://www.bnd.com/living/liv-columns-blogs/answer-man/article181298616.html)。

人们忽视的一点是，架构如何影响这些决策。

大约 20 年前，应用程序运行在由需要它们的公司拥有的计算机上——这就是所谓的“本地部署”。拥有这些计算机是一项架构决策。因此，云软件供应商并不存在，因为由于与当时架构的不兼容，云软件没有市场需求。

快进到 2024 年，情况正好相反——大多数人已经完全转向云计算。然而，我们中的一些人仍然在运营自己的服务器，另一些则采用混合模型。这意味着，理解架构对你选择的解决方案的影响比以往任何时候都更为重要，在本文中，我们将深入探讨微服务与单体架构在数据架构中对所购买工具的影响。

# 微服务与单体架构
