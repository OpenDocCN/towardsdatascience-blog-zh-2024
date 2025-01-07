# SQL：数据工程 — 第一部分

> 原文：[https://towardsdatascience.com/sql-mastering-data-engineering-essentials-part-i-693d683c5b54?source=collection_archive---------1-----------------------#2024-09-20](https://towardsdatascience.com/sql-mastering-data-engineering-essentials-part-i-693d683c5b54?source=collection_archive---------1-----------------------#2024-09-20)

## SQL在数据驱动决策中的应用

[](https://medium.com/@panData?source=post_page---byline--693d683c5b54--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--693d683c5b54--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--693d683c5b54--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--693d683c5b54--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--693d683c5b54--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--693d683c5b54--------------------------------) ·48分钟阅读·2024年9月20日

--

![](../Images/e5b41c545d1664ca6cc2ff0cb017dbb2.png)

照片由[Thao LEE](https://unsplash.com/@h4x0r3?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这里有一个有趣的事实：你知道SQL语言是什么时候创建的吗？它首次出现是什么时候？我知道！那是在**1974年**。那时我还没出生，人们已经在使用SQL了。今天，快50年过去了，它依然年轻如初。

SQL是**数据科学**领域中使用的主要工具之一。在某个时刻，你可能会用到SQL：无论是访问数据库、准备数据管道，还是加载数据。

无论你是在处理**数据科学任务**、**数据准备**还是**数据工程**，都不重要。SQL很可能会在某个时刻成为你工作的组成部分。任何从事数据工作的人，都会在某个项目中使用SQL。

我的目标是为你提供一个关于SQL的**完整介绍**，重点关注**数据工程**。我将介绍主要的命令，并逐步深入更复杂的主题，演示各种**动手操作**。

当然，在你日常的工作中，总会有接触到SQL的时候。没有办法避免，好吗？你必须将它加入到你的**工具箱**中。你知道的工具越多，你能解决的问题就越多。

另外，请尽量拍手👏👏，你能拍多少就拍多少，我为这篇文章付出了很多心血。走吧！🐼
