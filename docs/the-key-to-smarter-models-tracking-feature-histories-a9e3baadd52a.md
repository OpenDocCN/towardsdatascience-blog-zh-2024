# 更智能模型的关键：跟踪特征历史

> 原文：[`towardsdatascience.com/the-key-to-smarter-models-tracking-feature-histories-a9e3baadd52a?source=collection_archive---------3-----------------------#2024-12-31`](https://towardsdatascience.com/the-key-to-smarter-models-tracking-feature-histories-a9e3baadd52a?source=collection_archive---------3-----------------------#2024-12-31)

## 利用历史数据捕获上下文并改善预测

[](https://harrisonfhoffman.medium.com/?source=post_page---byline--a9e3baadd52a--------------------------------)![Harrison Hoffman](https://harrisonfhoffman.medium.com/?source=post_page---byline--a9e3baadd52a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a9e3baadd52a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a9e3baadd52a--------------------------------) [Harrison Hoffman](https://harrisonfhoffman.medium.com/?source=post_page---byline--a9e3baadd52a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a9e3baadd52a--------------------------------) ·8 分钟阅读·5 天前

--

![](img/4debcfcdf074c516baa0f90f97e226c7.png)

历史文本。来源：[`boudewijnhuijgens.getarchive.net/amp/media/bible-book-page-font-backgrounds-textures-49ea03`](https://boudewijnhuijgens.getarchive.net/amp/media/bible-book-page-font-backgrounds-textures-49ea03)

最近，我从数据科学的职业生涯稍微转向了一个更侧重工程的角色。我的团队正在构建一个高质量的数据仓库，以支持组织的商业智能（BI）和机器学习（ML）需求。我之所以选择这个职位，是因为我看到这是一个机会，能够运用我在数据科学岗位上获得的见解，影响数据仓库的设计和开发，并使其具有前瞻性。

在过去 6 年的几乎每一个数据科学职位中，我都注意到了一个共同的主题——数据基础设施的设计并未考虑到数据科学的需求。数据仓库/湖仓中的许多表格，通常是事实表和维度表，缺少构建高性能机器学习模型所需的关键字段或结构。我注意到的最普遍的限制是，大多数表格仅追踪观察的当前状态，而不是历史记录。

本文探讨了这个常见问题，并展示了如何通过一种名为“缓慢变化维度”的数据建模技术来解决它。阅读完本文后，你将了解存储历史数据对模型性能的影响，并掌握一些策略，帮助你在自己的应用场景中实施此方法。
