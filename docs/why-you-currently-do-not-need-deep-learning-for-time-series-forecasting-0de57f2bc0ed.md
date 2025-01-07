# 为什么你（目前）不需要深度学习进行时间序列预测

> 原文：[https://towardsdatascience.com/why-you-currently-do-not-need-deep-learning-for-time-series-forecasting-0de57f2bc0ed?source=collection_archive---------0-----------------------#2024-06-20](https://towardsdatascience.com/why-you-currently-do-not-need-deep-learning-for-time-series-forecasting-0de57f2bc0ed?source=collection_archive---------0-----------------------#2024-06-20)

## 你需要的是什么：来自马克里达基斯M5竞赛和2023年Kaggle AI报告的经验教训

[](https://medium.com/@jodancker?source=post_page---byline--0de57f2bc0ed--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--0de57f2bc0ed--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0de57f2bc0ed--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0de57f2bc0ed--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--0de57f2bc0ed--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0de57f2bc0ed--------------------------------) ·阅读时间：11分钟·2024年6月20日

--

![](../Images/2aaf1b5c74707730a64776d5915bb4c4.png)

图片由作者提供。

深度学习在时间序列预测中受到了广泛关注。许多文章和科学论文讨论了最新的深度学习模型，以及它如何比任何机器学习（ML）或统计模型更好。这给人一种深度学习将解决我们在时间序列预测中所有问题的印象，特别是对该领域的新手来说。

但根据我的经验，深度学习并不是你需要的东西。其他方法对于时间序列预测更为重要且效果更好。

因此，在本文中，我想向你展示哪些方法有效。我将展示那些在多种方式中已被验证有效的内容。我将使用马克里达基斯M5竞赛和2023年Kaggle AI报告的研究结果，并将其与我的经验进行对比。

马克里达基斯竞赛通过真实数据集比较预测方法，展示了哪些方法在实践中有效。自从近40年前竞赛开始以来，研究结果发生了变化。在前三届竞赛（M1至M3）中，统计模型主导了这一领域。而在M4竞赛中，机器学习（ML）模型开始展示出它们的潜力，表现形式为……
