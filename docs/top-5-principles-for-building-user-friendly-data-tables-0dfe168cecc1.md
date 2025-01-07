# 构建用户友好数据表的五大原则

> 原文：[`towardsdatascience.com/top-5-principles-for-building-user-friendly-data-tables-0dfe168cecc1?source=collection_archive---------1-----------------------#2024-10-13`](https://towardsdatascience.com/top-5-principles-for-building-user-friendly-data-tables-0dfe168cecc1?source=collection_archive---------1-----------------------#2024-10-13)

## 设计直观可靠的数据表，让你的数据团队喜爱

[](https://ydong029.medium.com/?source=post_page---byline--0dfe168cecc1--------------------------------)![Yu Dong](https://ydong029.medium.com/?source=post_page---byline--0dfe168cecc1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0dfe168cecc1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0dfe168cecc1--------------------------------) [于东](https://ydong029.medium.com/?source=post_page---byline--0dfe168cecc1--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0dfe168cecc1--------------------------------) ·阅读 7 分钟·2024 年 10 月 13 日

--

在数据科学和分析领域工作七年，我创建并查询了许多数据表。有很多次我会想，“这一列是什么意思？”“为什么在表 A 和表 B 中有两列同名？我应该使用哪一个？”“这个表的粒度是多少？”等等。

如果你也面临同样的挫折，这篇文章就是为你而写的！

在本文中，我将分享五个原则，这些原则将帮助你创建让同事们欣赏的数据表。请注意，这是从数据科学家的角度撰写的。因此，它不会涵盖传统数据库设计最佳实践，而是专注于制作用户友好表格的策略。

![](img/7389ac8d5b1b07029e727268f99634ac.png)

由 DALL·E 创建的图像

# I. 单一真相来源

为报告和分析维护每个关键数据点或指标的单一真相来源非常重要。不应该在多个表中重复逻辑。

为了方便起见，有时我们会在多个表中计算相同的指标。例如，`总交易价值（GMV）`的计算可能存在于客户表、月度财务报表、商家表中…
