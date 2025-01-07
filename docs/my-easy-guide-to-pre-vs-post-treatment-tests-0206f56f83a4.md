# 我的《前后处理测试简明指南》

> 原文：[`towardsdatascience.com/my-easy-guide-to-pre-vs-post-treatment-tests-0206f56f83a4?source=collection_archive---------4-----------------------#2024-06-13`](https://towardsdatascience.com/my-easy-guide-to-pre-vs-post-treatment-tests-0206f56f83a4?source=collection_archive---------4-----------------------#2024-06-13)

## 《前后测试简介与代码示例》

[](https://gustavorsantos.medium.com/?source=post_page---byline--0206f56f83a4--------------------------------)![Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--0206f56f83a4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0206f56f83a4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0206f56f83a4--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--0206f56f83a4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0206f56f83a4--------------------------------) ·13 分钟阅读·2024 年 6 月 13 日

--

![](img/5293904f7a64b21dd61ad915a9623a1b.png)

图片来自 [Towfiqu barbhuiya](https://unsplash.com/@towfiqu999999?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于 [Unsplash](https://unsplash.com/photos/a-calendar-with-red-push-buttons-pinned-to-it-bwOAixLG0uc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

我将从说起，A/B 测试对我来说一直不是一项强项。*“好吧，那如果你正在写一篇关于它的文章呢？”*你可能会这么想。

事实上，我不得不尽可能多地学习这个主题，寻找一种方法，在工作中快速顺利地执行这些测试。许多个月来，我一直避免进行这些测试，因为我无法找到任何能够立刻与我理解相契合的直接内容。

我读了许多关于 A/B 测试的概念性文章，其中一些是针对市场营销专业人员的，因此更多地涉及了如何确定样本量和测试周期的问题。但当谈到***前后测试***（也称为前后处理测试，我们可以说它是一种 A/B 测试类型）时，这些知识更难找到。

所以，希望这篇文章能够为所有寻求易于跟随的教程，使用 Python 执行这种类型测试的人提供一个良好的《前后测试》（或称前后测试）的介绍。

让我们深入了解一下。

# 概念
