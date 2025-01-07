# 如何创建合成数据

> 原文：[https://towardsdatascience.com/build-your-own-synthetic-data-15d91389a37b?source=collection_archive---------4-----------------------#2024-02-07](https://towardsdatascience.com/build-your-own-synthetic-data-15d91389a37b?source=collection_archive---------4-----------------------#2024-02-07)

## 从零开始使用Python创建完整的数据框

[](https://medium.com/@kurt.klingensmith?source=post_page---byline--15d91389a37b--------------------------------)[![Kurt Klingensmith](../Images/2249e99f12d10f81598c754b1aaf76cc.png)](https://medium.com/@kurt.klingensmith?source=post_page---byline--15d91389a37b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--15d91389a37b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--15d91389a37b--------------------------------) [Kurt Klingensmith](https://medium.com/@kurt.klingensmith?source=post_page---byline--15d91389a37b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--15d91389a37b--------------------------------) ·阅读时间：9分钟·2024年2月7日

--

![](../Images/0cdc29d0203d7225c4541733960f9b92.png)

图片来自[Joshua Sortino](https://unsplash.com/@sortino?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/worms-eye-view-photography-of-ceiling-LqKhnDzSF-8?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)上的作品。

在向《Towards Data Science》编辑团队提交最近的一篇文章后，我收到了一条简单的询问信息：这些数据集是否可以用于商业用途？这是一个很好的问题——我草稿中的数据集来自[Seaborn](https://seaborn.pydata.org)，这是一个常见的Python库，包含了17个示例数据集[1]。这些数据集看起来确实是开源的，而且，果然，许多数据集都可以轻松找到授权商业用途的许可证。遗憾的是，我恰好选择了其中一个没有找到许可证的数据集。但我决定不更换为另一个Seaborn数据集，而是自己制作合成数据。

## **什么是合成数据？**

IBM的Kim Martineau将合成数据定义为“在计算机上生成的信息，用于增强或替代真实数据，以改进AI模型、保护敏感数据并减少偏见”[2]。

合成数据可能*看起来*像是来自现实事件的信息，**但它并不是**。这样可以避免许可问题，隐藏专有数据，并保护个人信息。

合成数据不同于匿名化或掩码数据，后者通过改变某些字段，将来自实际事件的真实数据转变为无法追溯的数据。如果你在寻找……
