# Pandas 操作链式连接：优点与局限

> 原文：[`towardsdatascience.com/chaining-pandas-operations-strengths-and-limitations-3ac02bbaf482?source=collection_archive---------0-----------------------#2024-07-15`](https://towardsdatascience.com/chaining-pandas-operations-strengths-and-limitations-3ac02bbaf482?source=collection_archive---------0-----------------------#2024-07-15)

## PYTHON 编程

## 学习何时值得将 Pandas 操作链式连接。

[](https://medium.com/@nyggus?source=post_page---byline--3ac02bbaf482--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--3ac02bbaf482--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3ac02bbaf482--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3ac02bbaf482--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--3ac02bbaf482--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3ac02bbaf482--------------------------------) ·阅读时间：17 分钟·2024 年 7 月 15 日

--

![](img/29f78ce353d83e8fb57167f11bed3e7f.png)

图片由 [Dulcey Lima](https://unsplash.com/@dulceylima?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文的标题强调了 Pandas 操作链式连接的优点与局限——但说实话，我将写关于乐趣的内容。

为什么乐趣很重要？当我们有数据需要分析时，这真的重要吗？

我不知道对你而言什么有效，但对我来说，工作中的乐趣很重要。在我超过 20 年的数据科学经验中，我发现从编码中获得的乐趣越多，完成任务的满足感就越强。而我所说的乐趣，是指追求任务过程中的乐趣，而不仅仅是完成任务。当然，取得结果很重要，可能是最重要的。但相信我，如果你不喜欢你正在使用的工具，你做的唯一一件事就是尽可能快地完成工作。这可能导致错误，因为你可能会急于完成，忽视数据中的重要细节。而这是你希望避免的。

我从 R 语言转到 Python，使用 R 进行数据分析非常有趣——感谢 `dplyr` 语法。我一直很喜欢它，现在仍然如此。然而，当我转向 Python 时，我发现自己更喜欢它而非 R。我从未真正享受过在 R 中编程（请注意，分析数据和编程是两回事），而……
