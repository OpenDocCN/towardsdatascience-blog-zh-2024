# Python 无论你住在哪个国家，都能知道你的假期

> 原文：[https://towardsdatascience.com/python-could-know-your-holidays-no-matter-which-country-you-live-ea0f9ce61719?source=collection_archive---------10-----------------------#2024-02-12](https://towardsdatascience.com/python-could-know-your-holidays-no-matter-which-country-you-live-ea0f9ce61719?source=collection_archive---------10-----------------------#2024-02-12)

![](../Images/23d7c528c57ce66d6bb21e968a500248.png)

图片由[12019](https://pixabay.com/users/12019-12019/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1993704)提供，来自[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1993704)

## 获取任何国家、任何年份、任何日期的假期

[](https://christophertao.medium.com/?source=post_page---byline--ea0f9ce61719--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--ea0f9ce61719--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ea0f9ce61719--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ea0f9ce61719--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--ea0f9ce61719--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ea0f9ce61719--------------------------------) ·阅读时间9分钟·2024年2月12日

--

在我们的软件开发和数据分析工作中，我们喜欢可以精确建模的业务逻辑。然而，大多数时候，我们处理的现实世界充满了许多不遵循任何模式的规则。一个很好的例子就是公共假期。假期在不同国家甚至其子区域之间可能非常不同。如果我们的程序需要处理日期，很难避免假期。

当然，最好的解决方案是创建一个维度表，在后端数据库中更新它每年一次。然而，有时我们的程序需要处理大量不同国家，或者如果你的应用程序范围较小，存储所有假期在后端数据库中会有过高的开销。有时，我们的应用程序或数据分析环境甚至没有后端数据库。

在这种情况下，“Holidays”库可以成为我们的救星。它包含了来自141个不同国家的所有假期。此外，许多内置的有用技巧使它非常容易使用。让我在这篇文章中向你介绍这个神奇的库。

# 1\. 快速开始
