# 高级 SQL 用于数据科学

> 原文：[https://towardsdatascience.com/advanced-sql-for-data-science-43a045ae4143?source=collection_archive---------1-----------------------#2024-08-24](https://towardsdatascience.com/advanced-sql-for-data-science-43a045ae4143?source=collection_archive---------1-----------------------#2024-08-24)

## 提升你分析能力的专家技术

[](https://mshakhomirov.medium.com/?source=post_page---byline--43a045ae4143--------------------------------)[![💡Mike Shakhomirov](../Images/bc6895c7face3244d488feb97ba0f68e.png)](https://mshakhomirov.medium.com/?source=post_page---byline--43a045ae4143--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--43a045ae4143--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--43a045ae4143--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--43a045ae4143--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43a045ae4143--------------------------------) ·14 分钟阅读·2024年8月24日

--

![](../Images/868611f75741a42c9a61eb026995c2ba.png)

AI 生成的图像，使用 [Kandinsky](https://github.com/ai-forever/Kandinsky-2)

本故事深入探讨了对数据科学从业者有用的高级 SQL 技术。在本文中，我将详细介绍我在日常分析项目中使用的专家级 SQL 查询。SQL 与现代数据仓库共同构成了数据科学的基石。它是数据操作和用户行为分析中不可或缺的工具。我将要讲述的技术旨在从数据科学的角度提供实际和有益的应用。掌握 SQL 是一项宝贵的技能，对于广泛的项目至关重要，这些技术极大地简化了我的日常工作。我希望它对你也有所帮助。

鉴于 SQL 是数据仓库和商业智能专业人士使用的主要语言，它是跨数据平台共享数据的理想选择。其强大的功能促进了无缝的数据建模和可视化。它仍然是任何数据团队最受欢迎的沟通方式，也是市场上几乎所有数据平台的标准。

我们将使用 BigQuery 的标准 SQL 方言。运行我写的查询非常简单且免费，以下是我提供的 SQL 查询。

## 递归 CTE
