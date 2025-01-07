# 长尾的故事：为何营销组合模型中的不确定性很重要

> 原文：[https://towardsdatascience.com/a-story-of-long-tails-why-uncertainty-in-marketing-mix-modelling-is-important-95fc5a5eb94f?source=collection_archive---------7-----------------------#2024-11-27](https://towardsdatascience.com/a-story-of-long-tails-why-uncertainty-in-marketing-mix-modelling-is-important-95fc5a5eb94f?source=collection_archive---------7-----------------------#2024-11-27)

## “细节决定成败。值得等待，直到做对为止。” — 史蒂夫·乔布斯

[](https://javier-marin.medium.com/?source=post_page---byline--95fc5a5eb94f--------------------------------)[![Javier Marin](../Images/31800b2fbfd1f7c841c9f6a2579d5681.png)](https://javier-marin.medium.com/?source=post_page---byline--95fc5a5eb94f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--95fc5a5eb94f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--95fc5a5eb94f--------------------------------) [Javier Marin](https://javier-marin.medium.com/?source=post_page---byline--95fc5a5eb94f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--95fc5a5eb94f--------------------------------) ·阅读时间 23 分钟·2024年11月27日

--

![](../Images/e7c3cf42fd5629cbad2534f8e5c70ac8.png)

图片由作者使用 DALL-E 生成

如果你 **营销组合模型**（**MMM**）中最有价值的洞察隐藏在我们通常认为的不确定性或噪声中，会怎样呢？

如果我们能够从不同的角度来看待今天使用的 MMM 模型的结果，会发生什么呢？

我们这些使用贝叶斯分层模型构建 MMM 模型的人已经看到，这些模型¹为我们在模型中设置的每个参数提供了大量信息。通过应用严格且广泛验证的统计技术，我们选择例如后验分布的均值（有时是中位数）作为某个渠道影响力的值。然后，我们根据这个值进行思考并生成可操作的洞察。然而，事实是，贝叶斯分析输出的是一个值的概率分布，而**尾部**通常很大，包含**罕见事件和例外情况**。如果我们低估了这些尾部包含的信息，我们就会错失一个宝贵的机会。在这些长尾的表现中，如果我们用正确的视角去观察，就能发现非常有价值的洞察。实际上，大多数用户使用 MMM 模型的基本理念是量化…
