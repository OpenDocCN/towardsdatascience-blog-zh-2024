# Pandas：我参与贡献一个重要开源项目的经历

> 原文：[https://towardsdatascience.com/pandas-my-experience-contributing-to-a-major-open-source-project-f5ab6d8c3bc0?source=collection_archive---------7-----------------------#2024-04-19](https://towardsdatascience.com/pandas-my-experience-contributing-to-a-major-open-source-project-f5ab6d8c3bc0?source=collection_archive---------7-----------------------#2024-04-19)

## 开源

## 你也许值得参与其中

[](https://medium.com/@maclayton?source=post_page---byline--f5ab6d8c3bc0--------------------------------)[![Mike Clayton](../Images/2d37746b13b7d2ff1c6515893914da97.png)](https://medium.com/@maclayton?source=post_page---byline--f5ab6d8c3bc0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f5ab6d8c3bc0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f5ab6d8c3bc0--------------------------------) [Mike Clayton](https://medium.com/@maclayton?source=post_page---byline--f5ab6d8c3bc0--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f5ab6d8c3bc0--------------------------------) ·18 分钟阅读 ·2024年4月19日

--

![](../Images/75f11f2ced65fd5f87855b065d1134cb.png)

图片来源：[Markus Winkler](https://www.pexels.com/photo/smartphone-industry-writing-technology-12220470/) 在 [Pexels](https://www.pexels.com)

**开源项目通常依赖于众多人的贡献，以保持它们无错、安全、更新并不断向前发展。**

**这些人是谁？嗯，“他们”可能就是你我！除了花费一些时间和精力外，没有任何东西能阻止我们贡献。所以，值得我们花时间吗？如果我们决定尝试一下，究竟涉及些什么呢？**

**我决定参与贡献一个广为人知且被广泛使用的开源库——** [***Pandas***](https://pandas.pydata.org/)**.**

**希望我在这个过程中所经历的能够为你提供一些关于所涉及内容的见解，并可能突出一种你也许能够参与其中的方式。或者，至少它能够让你对幕后发生的事情有一个很好的了解！**

# 介绍

在我之前的一篇 [文章](/matplotlib-make-your-plotting-life-easier-with-rcparams-d88f202fa83c) 中，我提到了使用 pandas 库生成 [箱线图](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.boxplot.html) 时遇到的一个 bug。

我最初查找了这个 bug，看看是否有其他人遇到同样的问题。我发现了一个 stackoverflow 的帖子，看起来也正是我遇到的那个问题：
