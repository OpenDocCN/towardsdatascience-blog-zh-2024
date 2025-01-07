# 你不知道的Python Secrets

> 原文：[https://towardsdatascience.com/the-secrets-of-python-secrets-05e00ad426a2?source=collection_archive---------4-----------------------#2024-01-21](https://towardsdatascience.com/the-secrets-of-python-secrets-05e00ad426a2?source=collection_archive---------4-----------------------#2024-01-21)

![](../Images/4141616fabf1b0cfe9c08e344d008306.png)

图片由[Tayeb MEZAHDIA](https://pixabay.com/users/tayebmezahdia-4194100/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3037639)提供，来自[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3037639)

## 为什么以及在何时我们不应使用“random”，而应使用“secrets”？

[](https://christophertao.medium.com/?source=post_page---byline--05e00ad426a2--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--05e00ad426a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--05e00ad426a2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--05e00ad426a2--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--05e00ad426a2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--05e00ad426a2--------------------------------) ·阅读时长：7分钟·2024年1月21日

--

如果你从事数据科学和分析领域的工作，我猜你一定像我一样经常使用Python中的`random`模块。的确，在我们需要进行一些模拟、数据采样和各种其他统计算法时，它非常有用。

然而，Python中还有一个叫做`secrets`的内置模块，我相信它的知名度远不如`random`。它几乎可以做和`random`一样的事情，甚至有些函数的名称完全相同。

为什么会有这样一个模块？我们什么时候应该使用`secrets`而不是`random`？请继续阅读本文以获取答案。

在开始之前，与我其他大多数文章不同，这篇文章没有“安装”部分。`secrets`模块是Python内置的。所以，我们可以直接使用它，不必担心安装问题。

# 1. 安全生成随机数

![](../Images/ad3d140295ab78f5a49109dd47e1946e.png)

图片由[AndreasAux](https://pixabay.com/users/andreasaux-148268/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=442544)提供，来自[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=442544)

让我们先看看Secrets模块中的一些基本随机函数。然后，我会解释为什么在某些场景下我们不应该使用`random`。
