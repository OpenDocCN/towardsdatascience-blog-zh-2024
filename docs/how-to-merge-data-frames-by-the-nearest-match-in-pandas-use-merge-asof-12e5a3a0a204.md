# 如何在 Pandas 中通过最近匹配合并数据框？使用 `merge_asof`。

> 原文：[https://towardsdatascience.com/how-to-merge-data-frames-by-the-nearest-match-in-pandas-use-merge-asof-12e5a3a0a204?source=collection_archive---------4-----------------------#2024-02-18](https://towardsdatascience.com/how-to-merge-data-frames-by-the-nearest-match-in-pandas-use-merge-asof-12e5a3a0a204?source=collection_archive---------4-----------------------#2024-02-18)

## PANDAS

## 这篇简短的文章介绍了 Pandas 中一个有用的函数 `merge_asof`。它是处理时间序列数据时 Pandas 中最常用的工具之一。

[](https://jianan-lin.medium.com/?source=post_page---byline--12e5a3a0a204--------------------------------)[![Yufeng](../Images/8b1a4c165aaac045ea819f850017b7cd.png)](https://jianan-lin.medium.com/?source=post_page---byline--12e5a3a0a204--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--12e5a3a0a204--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--12e5a3a0a204--------------------------------) [Yufeng](https://jianan-lin.medium.com/?source=post_page---byline--12e5a3a0a204--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12e5a3a0a204--------------------------------) ·阅读时长 5 分钟 ·2024年2月18日

--

![](../Images/79c188ebf09394b6df392cd725d66ef9.png)

图片来源：[Stephen Phillips - Hostreviews.co.uk](https://unsplash.com/@hostreviews?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

合并数据框是数据科学中最常见的操作之一。大多数数据合并操作侧重于精确合并，即左右数据框中的一行必须具有相同的索引/值。然而，有时我们并不需要精确匹配，而是需要在合并数据框时寻找最近的匹配，特别是在时间序列分析中。

例如，我们有一个记录每日标准普尔500指数的 DataFrame，还有一个记录纽约市每日天气的 DataFrame。我们想知道纽约市的天气是否会影响第二天的标准普尔500指数。

请注意，市场在周末和节假日关闭，因此我们需要确保每个交易日的标准普尔500指数所对应的天气信息是最新的工作日信息。

为了完成上述任务，我们需要使用 Pandas 中的一个函数 `merge_asof`，而不是 `merge`。

在这篇简短的文章中，我将简要介绍如何使用 Python 中的这个函数，并附上代码。希望对你有帮助。
