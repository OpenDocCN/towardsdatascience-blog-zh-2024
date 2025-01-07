# 概率分布：泊松分布与二项分布

> 原文：[https://towardsdatascience.com/probability-distributions-poisson-vs-binomial-distribution-ff8a6ddeb4a1?source=collection_archive---------0-----------------------#2024-12-10](https://towardsdatascience.com/probability-distributions-poisson-vs-binomial-distribution-ff8a6ddeb4a1?source=collection_archive---------0-----------------------#2024-12-10)

## 使用足球来理解泊松分布与二项分布的区别：数据科学系列（3）

[](https://medium.com/@sahn1998?source=post_page---byline--ff8a6ddeb4a1--------------------------------)[![Sunghyun Ahn](../Images/e8efe3b5d7f0267713ab66149a8de5f7.png)](https://medium.com/@sahn1998?source=post_page---byline--ff8a6ddeb4a1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ff8a6ddeb4a1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ff8a6ddeb4a1--------------------------------) [Sunghyun Ahn](https://medium.com/@sahn1998?source=post_page---byline--ff8a6ddeb4a1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ff8a6ddeb4a1--------------------------------) ·10分钟阅读·2024年12月10日

--

![](../Images/0c692febcdc5af6a6129de75432adb75.png)

图片由 [Fancy Crave](https://unsplash.com/@fancycrave?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/people-watching-soccer-arena-qowyMze7jqg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

> 如果你不是 Medium 的付费会员，我将我的文章免费提供给你：[朋友链接](https://medium.com/@sahn1998/probability-distributions-poisson-vs-binomial-distribution-ff8a6ddeb4a1?sk=8d24d6bf734a36bb10111e2a85d5317a)

**泊松分布**与**二项分布**是数据科学和统计学中两种基本的离散概率分布。虽然它们都涉及计数事件或成功次数，但它们在基本假设和使用场景上有所不同。

如果你像我一样，可能有时会疑惑是该使用泊松分布还是二项分布来分析问题——特别是如果你最近没有使用过它们的话。

为了澄清这一点，让我们深入了解每种分布的特征、何时使用它们以及如何区分这两者。

# 目录

1.  **泊松分布**

1.  **二项分布**

1.  **泊松分布与二项分布：有什么区别？**

1.  **简而言之**

# 泊松分布：一个足球实例
