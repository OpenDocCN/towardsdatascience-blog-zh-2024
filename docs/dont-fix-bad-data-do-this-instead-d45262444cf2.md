# 不要修复坏数据，改做这样做

> 原文：[https://towardsdatascience.com/dont-fix-bad-data-do-this-instead-d45262444cf2?source=collection_archive---------3-----------------------#2024-01-31](https://towardsdatascience.com/dont-fix-bad-data-do-this-instead-d45262444cf2?source=collection_archive---------3-----------------------#2024-01-31)

## 人们在谈论数据质量时，并不清楚他们到底在说什么。

[](https://medium.com/@ivanicova?source=post_page---byline--d45262444cf2--------------------------------)[![Martina Ivaničová](../Images/a7d2687449b212798414c0234ef48c39.png)](https://medium.com/@ivanicova?source=post_page---byline--d45262444cf2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d45262444cf2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d45262444cf2--------------------------------) [Martina Ivaničová](https://medium.com/@ivanicova?source=post_page---byline--d45262444cf2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d45262444cf2--------------------------------) ·7分钟阅读·2024年1月31日

--

![](../Images/dc77e2cce1395ae606ea5fcfe0820503.png)

图片来源：[No Revisions](https://unsplash.com/@norevisions?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **来自战壕的故事**

几年前，我们的数据平台团队旨在找出数据用户的主要关切点。我们对使用我们数据平台的人员进行了调查，结果并不意外，最主要的关切是数据质量。

我们工程思维模式下的初步反应是开发数据质量工具。我们引入了一个名为Contessa的内部工具。尽管Contessa使用起来略显繁琐，并且需要大量的手动配置，但它帮助进行数据质量的标准维度检查，涵盖了一致性、时效性、有效性、唯一性、准确性和完整性。在运行了几个月，进行了数百次数据质量检查之后，我们得出结论：

+   数据质量检查偶尔帮助数据用户在较短的时间内发现数据已被破坏，无法依赖。

+   尽管频繁执行数据质量检查，但在数据质量的主观感知上并没有显著改善。

+   对于大部分问题，特别是通过自动化数据质量检查发现的问题...
