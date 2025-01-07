# 关心，还是不关心：使用 XmR 图表区分度量中的信号与噪声

> 原文：[https://towardsdatascience.com/to-care-or-not-to-care-using-xmr-charts-to-differentiate-signals-from-noise-in-metrics-91f6980dd8e6?source=collection_archive---------14-----------------------#2024-09-10](https://towardsdatascience.com/to-care-or-not-to-care-using-xmr-charts-to-differentiate-signals-from-noise-in-metrics-91f6980dd8e6?source=collection_archive---------14-----------------------#2024-09-10)

## 创建和解读 XmR 图表以进行有效数据分析的逐步指南

[](https://miqbalrp.medium.com/?source=post_page---byline--91f6980dd8e6--------------------------------)[![Iqbal Rahmadhan](../Images/778d9cb795065223ec8fa53001de1815.png)](https://miqbalrp.medium.com/?source=post_page---byline--91f6980dd8e6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--91f6980dd8e6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--91f6980dd8e6--------------------------------) [Iqbal Rahmadhan](https://miqbalrp.medium.com/?source=post_page---byline--91f6980dd8e6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--91f6980dd8e6--------------------------------) ·阅读时间：11 分钟·2024 年 9 月 10 日

--

在数据驱动的组织中，跟踪指标对于做出正确的商业决策至关重要。当你需要关注的关键指标不多时，审查过程通常很简单。但随着公司规模的增长，指标的数量和复杂性也会增加，导致事情变得更加复杂。

![](../Images/7bd8102e18181d957da63319362956da.png)

图片由 [Félix Prado](https://unsplash.com/@fprado?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你是否曾经遇到过某一周的指标出现突增，让你怀疑它的意义？或者当某个指标突然下降时，你会想知道是否值得担忧？区分真正的趋势和纯粹的随机噪声并不总是那么容易。

> **还不是 Medium 会员？使用这个** [**好友链接**](/to-care-or-not-to-care-using-xmr-charts-to-differentiate-signals-from-noise-in-metrics-91f6980dd8e6?sk=bc05996e409f0676dc74ff52b0967eb9) **继续阅读本文。**

这就是 XmR 图表（个体图和移动范围图）发挥作用的地方。这些图表对于发现模式和变化非常有用，帮助数据分析师和相关人员迅速判断哪些变化值得深入研究。

# 什么是 XmR 图表？

XmR 图表，简写为“个体图（X）和移动范围图（mR）”，是一种强大的工具…
