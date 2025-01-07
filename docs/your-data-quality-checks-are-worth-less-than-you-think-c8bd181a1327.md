# 你的数据质量检查远没有你想象的那么有效

> 原文：[https://towardsdatascience.com/your-data-quality-checks-are-worth-less-than-you-think-c8bd181a1327?source=collection_archive---------7-----------------------#2024-11-20](https://towardsdatascience.com/your-data-quality-checks-are-worth-less-than-you-think-c8bd181a1327?source=collection_archive---------7-----------------------#2024-11-20)

## 如何在你的数据质量项目中创造超额价值

[](https://medium.com/@cisenbe?source=post_page---byline--c8bd181a1327--------------------------------)[![Chad Isenberg](../Images/56e50c1ee292ac672df4b8062e460c8e.png)](https://medium.com/@cisenbe?source=post_page---byline--c8bd181a1327--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c8bd181a1327--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c8bd181a1327--------------------------------) [Chad Isenberg](https://medium.com/@cisenbe?source=post_page---byline--c8bd181a1327--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c8bd181a1327--------------------------------) ·5分钟阅读·2024年11月20日

--

![](../Images/7751c0797a268fec4df4da0638c9af5e.png)

图片来源：[Wolfgang Weiser](https://unsplash.com/@hamburgmeinefreundin?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在过去的几年里，数据质量和可观察性成为了热门话题。这个领域有着大量的解决方案（按顺序排列，不完全列举）：

+   [dbt 测试](https://docs.getdbt.com/docs/build/data-tests)

+   [SQLMesh 审计](https://sqlmesh.readthedocs.io/en/latest/concepts/audits/)

+   [Monte Carlo](https://www.montecarlodata.com/)

+   [Great Expectations](https://greatexpectations.io/)

+   [Soda](https://www.soda.io/)

+   [Sifflet](https://www.siffletdata.com/)

无论这些工具的具体特性如何，它们都有一个共同的目标：提高数据质量问题的可视化，减少数据事件的数量，并增加信任。然而，尽管进入门槛较低，数据质量程序仍然很难成功实施。我相信有三个简单的改进措施可以提升你的成果。让我们深入了解！

## 提示 1：专注于流程失败，而不是坏记录（在可以的情况下）

对于工程思维的人来说，可能很难接受一些“坏”记录不仅会流入你的系统，甚至会*穿过*你的系统，而这可能是可以接受的！请考虑以下几点：

1.  坏记录在源头修正后会被清除吗…
