# 足球与几何学 — 传球网络

> 原文：[https://towardsdatascience.com/football-and-geometry-passing-networks-6e201ceff6ef?source=collection_archive---------12-----------------------#2024-09-16](https://towardsdatascience.com/football-and-geometry-passing-networks-6e201ceff6ef?source=collection_archive---------12-----------------------#2024-09-16)

## 足球分析

## 通过分析拜耳勒沃库森的传球网络来理解网络

[](https://polmarin.medium.com/?source=post_page---byline--6e201ceff6ef--------------------------------)[![Pol Marin](../Images/a4f69a96717d453db9791f27b8f85e86.png)](https://polmarin.medium.com/?source=post_page---byline--6e201ceff6ef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6e201ceff6ef--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6e201ceff6ef--------------------------------) [Pol Marin](https://polmarin.medium.com/?source=post_page---byline--6e201ceff6ef--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e201ceff6ef--------------------------------) ·10分钟阅读·2024年9月16日

--

![](../Images/6527465fc26c9ff2f885cb81b4cbbab0.png)

图片来源：[Clint Adair](https://unsplash.com/@clintadair?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

好久不见…但有一个好的理由。

经过几个月的休息，我回到 Medium，今天我们将融合两个令人兴奋的领域：足球与几何学。

具体来说，我们将讨论网络这一主题，但像往常一样，通过一个实际案例。我们将研究足球传球网络，重点分析去年拜耳勒沃库森的比赛。

这支德甲冠军队在哈维·阿隆索的带领下度过了一个精彩的赛季，踢出了令人惊叹的足球。我很好奇如何将这些转化为数学术语，通过他们的传球网络理解他们的踢球风格和最具代表性的球员。

尽管网络在研究节点之间的互联互通方面的重要性已经确立，但它在足球中的应用并没有不同。实际上，这是基础知识，但值得为那些还没有接触过的人写一篇文章。

Statsbomb[1] 提供了高质量的数据，幸运的是，他们免费并公开了上赛季所有拜耳勒沃库森的比赛数据。
