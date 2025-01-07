# 来自 COVID-19 的教训：为什么概率分布如此重要

> 原文：[`towardsdatascience.com/why-probability-distributions-matter-lessons-from-covid-19-9306b535a5c8?source=collection_archive---------3-----------------------#2024-12-27`](https://towardsdatascience.com/why-probability-distributions-matter-lessons-from-covid-19-9306b535a5c8?source=collection_archive---------3-----------------------#2024-12-27)

## 用*极值*理解分布：数据科学系列（完结）

[](https://medium.com/@sahn1998?source=post_page---byline--9306b535a5c8--------------------------------)![Sunghyun Ahn](https://medium.com/@sahn1998?source=post_page---byline--9306b535a5c8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9306b535a5c8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9306b535a5c8--------------------------------) [Sunghyun Ahn](https://medium.com/@sahn1998?source=post_page---byline--9306b535a5c8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9306b535a5c8--------------------------------) ·阅读时长 11 分钟·2024 年 12 月 27 日

--

![](img/ec8b24577d88c235d6456a971d60568c.png)

图片由 [Glen Carrie](https://unsplash.com/@glencarrie?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/coronavirus-on-black-background-LfviizMGKaE?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

> 如果你不是 Medium 的付费会员，我会将我的文章免费提供给大家：[朋友链接](https://medium.com/@sahn1998/why-probability-distributions-matter-lessons-from-covid-19-9306b535a5c8?sk=a7f89df086fa62d53100e0ed955143d6)

如果你一直在关注[我的文章](https://medium.com/@sahn1998/basics-of-probability-distributions-pmf-pdf-cdfs-a0590ef1e8ba)，你可能已经注意到我最近对**概率分布**的强调。我花了很多时间讲解它们的重要性，这绝不是没有理由的。如果你已经理解了这些分布为何至关重要，这篇文章将为你提供很好的补充。如果还没有，我希望这篇文章能为你提供一些新的见解！

> 让我问你一个问题

**为什么概率分布如此重要？** 为什么我们要花这么多时间研究概率密度函数（PDF）和累积分布函数（CDF）？*提示：答案取决于你问谁。*

然而，你可能是为了更直接的答案而来的。所以……对我来说……我会通过帮助你理解极值像 Xₘᵢₙ 和 Xₘₐₓ 来回答这个问题。*(如果你只想要答案，请跳到文章结尾)*！

希望我的解释既直观又易于理解，没有多余的行话。
