# Python 十亿行挑战——从 10 分钟到 4 秒

> 原文：[https://towardsdatascience.com/python-one-billion-row-challenge-from-10-minutes-to-4-seconds-0718662b303e?source=collection_archive---------0-----------------------#2024-05-08](https://towardsdatascience.com/python-one-billion-row-challenge-from-10-minutes-to-4-seconds-0718662b303e?source=collection_archive---------0-----------------------#2024-05-08)

## 十亿行挑战正在迅速流行。那么 Python 的表现如何呢？

[](https://medium.com/@radecicdario?source=post_page---byline--0718662b303e--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--0718662b303e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0718662b303e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0718662b303e--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--0718662b303e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0718662b303e--------------------------------) ·10 分钟阅读 ·2024年5月8日

--

![](../Images/5a1a30d771e7c1dbc76b57b76b2635ac.png)

图片由[Alina Grubnyak](https://unsplash.com/@alinnnaaaa?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

关于编程语言处理和聚合[10亿行](https://1brc.dev)数据的速度问题，最近引起了广泛关注。**Python** 并不是最具性能的语言，因此自然**没有机会**——尤其是目前[表现最优秀的 Java 实现](https://www.morling.dev/blog/1brc-results-are-in/)仅需 1.535 秒！

挑战的基本[规则](https://1brc.dev/#rules-and-limits)是**不允许使用外部库**。我今天的目标是首先遵守规则，然后看看如果使用外部库和更合适的文件格式会发生什么。

**我已运行所有脚本 5 次，并对结果进行了平均。**

至于硬件，我使用的是*16 英寸 M3 Pro Macbook Pro，配有 12 个 CPU 核心和 36 GB 的内存*。如果你决定运行代码，你的结果可能会有所不同，但希望你能看到各个实现之间的类似百分比差异。

## 代码

+   [GitHub 仓库 — Python 中的 10 亿行挑战](https://github.com/darioradecic/python-1-billion-row-challenge/blob/main/data/convertToParquet.py)

# 什么是十亿行挑战？

十亿行挑战（1BRC）的核心理念很简单——通过一个 `.txt` 文件…
