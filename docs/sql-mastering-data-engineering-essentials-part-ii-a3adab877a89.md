# SQL：数据工程——第二部分。

> 原文：[https://towardsdatascience.com/sql-mastering-data-engineering-essentials-part-ii-a3adab877a89?source=collection_archive---------8-----------------------#2024-10-01](https://towardsdatascience.com/sql-mastering-data-engineering-essentials-part-ii-a3adab877a89?source=collection_archive---------8-----------------------#2024-10-01)

## 用SQL进行数据驱动决策

[](https://medium.com/@panData?source=post_page---byline--a3adab877a89--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--a3adab877a89--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a3adab877a89--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a3adab877a89--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--a3adab877a89--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a3adab877a89--------------------------------) ·42分钟阅读·2024年10月1日

--

![](../Images/d692699bf894b249da23c4e318ecfaf8.png)

图片由[SOON SANTOS](https://unsplash.com/@soonsam?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**非常感谢你至今与我同行！** 本系列的第一部分为我们打下了基础，现在是时候更深入地探索了。

在下一个阶段，我们将超越之前的`sc01`模式，开始攻克更高级的数据工程概念。

我真心感谢你在这段旅程中给予的所有支持和鼓励。

你的反馈和热情让我保持灵感，并激励我继续与大家一起探索这些话题。让我们保持这股动力继续前进！👏👏

[](/sql-mastering-data-engineering-essentials-part-i-693d683c5b54?source=post_page-----a3adab877a89--------------------------------) [## SQL：掌握数据工程基本要点（第一部分）

### 解锁SQL在数据驱动决策中的强大力量

towardsdatascience.com](/sql-mastering-data-engineering-essentials-part-i-693d683c5b54?source=post_page-----a3adab877a89--------------------------------)

现在，使用新的`schema`，让我们准备数据库，创建表格，插入数据，并继续前进。

![](../Images/d082b8d2764727d154523280a5b046c4.png)![](../Images/195f44307c19c844dd160d111a7a7d00.png)

右键点击“Schemas”，打开查询工具，我们将看到一个空白区域，如右侧图片所示，从而重新开始我们的旅程。
