# 使用 Isolation Forest 和 SHAP 解释异常

> 原文：[`towardsdatascience.com/explaining-anomalies-with-isolation-forest-and-shap-0d5d1224b918?source=collection_archive---------2-----------------------#2024-09-30`](https://towardsdatascience.com/explaining-anomalies-with-isolation-forest-and-shap-0d5d1224b918?source=collection_archive---------2-----------------------#2024-09-30)

## Isolation Forest 是一种无监督的基于树的异常检测方法。请查看 KernelSHAP 和 TreeSHAP 如何被用来解释其输出。

[](https://conorosullyds.medium.com/?source=post_page---byline--0d5d1224b918--------------------------------)![Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--0d5d1224b918--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0d5d1224b918--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d5d1224b918--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--0d5d1224b918--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d5d1224b918--------------------------------) ·阅读时间 13 分钟·2024 年 9 月 30 日

--

![](img/b3af57d81f5593ea564511d46da28df6.png)

图片由[Fabrice Villard](https://unsplash.com/@fabulu75?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

[Isolation Forest](https://www.datacamp.com/tutorial/isolation-forest)已经成为异常检测系统中的重要工具[1]。它的优势在于能够在具有多个特征的大型数据集中找到复杂的异常。然而，当涉及到解释这些异常时，这一优势很快就变成了一个弱点。

要对异常采取行动，我们通常需要理解为什么它会被归类为异常。这种洞察在实际应用中尤为重要，例如在欺诈检测中，了解异常背后的原因通常与发现异常本身一样重要。

不幸的是，使用 Isolation Forest 时，这些解释被隐藏在复杂的模型结构中。为了揭示它们，我们转向 SHAP。

我们将应用 SHAP 来解释 IsolationForest，并解读其输出。我们将看到，尽管这是一种无监督模型，我们仍然可以使用 SHAP 来解释其异常评分。也就是说，理解：

+   特征如何对单个实例的评分产生影响

+   以及哪些特征在总体上较为重要。
