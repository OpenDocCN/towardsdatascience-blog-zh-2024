# 模型选择：类别平衡 第1部分

> 原文：[https://towardsdatascience.com/model-selection-a-guide-to-class-balancing-part-i-14b17003186f?source=collection_archive---------7-----------------------#2024-10-10](https://towardsdatascience.com/model-selection-a-guide-to-class-balancing-part-i-14b17003186f?source=collection_archive---------7-----------------------#2024-10-10)

## 关于匿名化数据类别平衡的教程。

[](https://medium.com/@panData?source=post_page---byline--14b17003186f--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--14b17003186f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--14b17003186f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--14b17003186f--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--14b17003186f--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--14b17003186f--------------------------------) ·阅读时间22分钟·2024年10月10日

--

![](../Images/eafda58e4862c6ca81681b8e40ac8439.png)

图片由[Dave Lowe](https://unsplash.com/@davelowephoto?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我将为你带来一个**机器学习模型选择**项目，涉及**匿名化数据的多变量分析**。

这是一个全面的项目，我们将从头到尾进行讲解——从定义业务问题到模型部署（尽管部署部分我们会留到其他时间）。

这个项目将有两部分完整的教程，我希望带你了解一系列技术，其中包括处理**匿名化数据**的复杂性——这是由于数据隐私问题，越来越多地出现在职场中的一种情况。

[](https://github.com/Anello92/Machine-Learning-Model-Selection-and-Class-Balancing?source=post_page-----14b17003186f--------------------------------) [## GitHub - Anello92/Model-Selection-and-Class-Balancing

### 本仓库包含一个关于机器学习模型选择的全面教程，专注于多变量分析...

github.com](https://github.com/Anello92/Machine-Learning-Model-Selection-and-Class-Balancing?source=post_page-----14b17003186f--------------------------------)

那么，处理这种类型的数据的最大挑战是什么呢？就是你没有任何关于每个变量所代表的内容的信息。

现在，这可有点棘手，不是吗？你会收到数据，**在不知道**每个变量代表什么的情况下，你需要基于这些数据开发一个**机器学习模型**。

我们还将借此机会深入探讨**模型选择**。哪种机器...
