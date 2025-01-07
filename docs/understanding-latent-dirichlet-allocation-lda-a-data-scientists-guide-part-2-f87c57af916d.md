# 理解潜在狄利克雷分配（LDA）——数据科学家的指南（第二部分）

> 原文：[https://towardsdatascience.com/understanding-latent-dirichlet-allocation-lda-a-data-scientists-guide-part-2-f87c57af916d?source=collection_archive---------5-----------------------#2024-02-19](https://towardsdatascience.com/understanding-latent-dirichlet-allocation-lda-a-data-scientists-guide-part-2-f87c57af916d?source=collection_archive---------5-----------------------#2024-02-19)

![](../Images/1e7a677eb2d1a17ae71ef1f2125c1162.png)

除非另有说明，否则所有图表和图片均为作者所作（图像由作者使用MidJourney生成，已获得付费许可）

## LDA收敛性解释：通过狗血统模型

[](https://louis-chan.medium.com/?source=post_page---byline--f87c57af916d--------------------------------)[![Louis Chan](../Images/6d8df9a478e929dd521059631f26e081.png)](https://louis-chan.medium.com/?source=post_page---byline--f87c57af916d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f87c57af916d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f87c57af916d--------------------------------) [Louis Chan](https://louis-chan.medium.com/?source=post_page---byline--f87c57af916d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f87c57af916d--------------------------------) ·10分钟阅读·2024年2月19日

--

> “如果我对狗品种分布的先验理解不准确，那我的LDA模型是否就注定失败？”

我的妻子问道。

欢迎回到本系列的第二部分，在这一部分，我将分享如何向我的妻子解释LDA的经历。在上一篇博客中，我们讨论了LDA是如何工作的，并且如何将其理解为一种狗的血统模型。

这次我们将深入探讨LDA的迭代拟合过程！

**第一部分（**[**链接**](/understanding-latent-dirichlet-allocation-lda-a-data-scientists-guide-part-1-905f8dc64ab6)**）：**

+   LDA是如何工作的？

+   如何向非技术人员解释LDA？

**第二部分（我们现在就在这里！）：**

+   LDA是如何在迭代中改进的？

+   LDA是如何收敛的？

+   奖励：在这里获取你的LDA备忘单！

**第三部分：**

+   什么时候使用LDA，什么时候不使用？

+   我们如何在Python中使用它？

+   LDA有哪些替代方案和变种（不包括LLM）？
