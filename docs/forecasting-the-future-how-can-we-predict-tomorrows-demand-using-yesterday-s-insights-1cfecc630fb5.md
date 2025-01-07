# 预测未来：我们如何利用昨天的见解预测明天的需求？

> 原文：[https://towardsdatascience.com/forecasting-the-future-how-can-we-predict-tomorrows-demand-using-yesterday-s-insights-1cfecc630fb5?source=collection_archive---------9-----------------------#2024-11-05](https://towardsdatascience.com/forecasting-the-future-how-can-we-predict-tomorrows-demand-using-yesterday-s-insights-1cfecc630fb5?source=collection_archive---------9-----------------------#2024-11-05)

## 尽管人工智能模型成为了焦点，传统的统计模型仍然是需求预测中非常有价值的工具

[](https://medium.com/@ayoubeloutati?source=post_page---byline--1cfecc630fb5--------------------------------)[![Ayoub El Outati](../Images/e26bdef5b21c47e8525310ee894913ad.png)](https://medium.com/@ayoubeloutati?source=post_page---byline--1cfecc630fb5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1cfecc630fb5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1cfecc630fb5--------------------------------) [Ayoub El Outati](https://medium.com/@ayoubeloutati?source=post_page---byline--1cfecc630fb5--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1cfecc630fb5--------------------------------) ·11分钟阅读·2024年11月5日

--

![](../Images/c0a9384c3277d0b2c1b2433a9f618b99.png)

图片由[petr sidorov](https://unsplash.com/@m_malkovich?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你好，Medium的读者们！

今天，我们将深入探讨应用于**需求规划**的**预测技术**，这是我非常关注的领域，因为我有供应链背景，并且对数据科学充满热情。最近，我一直在阅读有关这个主题的书籍和文章，重新审视**需求预测**，以便为你提供一些新的见解。

首先，让我分享一句发人深省的名言，来自英国统计学家**George E. P. Box：**

> **“所有模型都是错误的，但有些是有用的。”**

当你思考这句话时，你可能会想：如果没有任何模型能完全准确，为什么还要预测未来呢？可以把它想象成天气预报：它帮助我们提前规划。我明天要带伞吗？我要擦防晒霜吗？我需要躲避飓风吗？**虽然预测不完美，但它们帮助我们做出更好的决策。**

在**需求规划**中也不例外。**需求规划师和其他公司利益相关者利用预测来预判未来需求并做出调整**…
