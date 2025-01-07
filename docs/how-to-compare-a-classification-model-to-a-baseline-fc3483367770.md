# 如何将分类模型与基线模型进行比较

> 原文：[https://towardsdatascience.com/how-to-compare-a-classification-model-to-a-baseline-fc3483367770?source=collection_archive---------8-----------------------#2024-02-18](https://towardsdatascience.com/how-to-compare-a-classification-model-to-a-baseline-fc3483367770?source=collection_archive---------8-----------------------#2024-02-18)

## 数据科学，机器学习

## 一份可以直接运行的 Python 和 scikit-learn 教程，用于评估分类模型与基线模型的对比

[](https://alod83.medium.com/?source=post_page---byline--fc3483367770--------------------------------)[![Angelica Lo Duca](../Images/45aa2e2e504bb3af6d3b8009dc6f030e.png)](https://alod83.medium.com/?source=post_page---byline--fc3483367770--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc3483367770--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fc3483367770--------------------------------) [Angelica Lo Duca](https://alod83.medium.com/?source=post_page---byline--fc3483367770--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc3483367770--------------------------------) ·6分钟阅读·2024年2月18日

--

![](../Images/daf0af15dbadd313f17253df0fb5a24f.png)

图片来源：[Kier in Sight Archives](https://unsplash.com/@kierinsightarchives?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

前几天，我需要判断我的分类算法的表现是否令人满意。我获得了 64% 的精确度、召回率和准确率，老实说，我以为这是一个糟糕的结果。事实上，64% 稍微好于一个随机模型。实际上，如果问题本身较简单，这个结论是成立的。例如，在只有两个值的情况下，随机算法有 50% 的概率预测正确结果。因此，在这种情况下，准确率为 64% 的算法比随机算法要好。

如果你正在处理一个多类别算法，其中类别的数量大于两个，那么问题就不同了。在我的例子中，我有大约 1000 个类别。在这种情况下，问题比二分类问题复杂得多，因此 64% 的准确率可能甚至是一个不错的算法！

那么，如何判断获得的模型性能是否令人满意呢？解决方案是将模型与一个代表该问题的虚拟模型进行比较。如果我们的模型表现优于虚拟模型，那么结果是有希望的……
