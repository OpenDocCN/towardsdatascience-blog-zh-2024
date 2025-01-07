# 数据泄露的常见原因及如何识别

> 原文：[`towardsdatascience.com/common-causes-of-data-leakage-and-how-to-spot-them-17113406f9f8?source=collection_archive---------7-----------------------#2024-05-17`](https://towardsdatascience.com/common-causes-of-data-leakage-and-how-to-spot-them-17113406f9f8?source=collection_archive---------7-----------------------#2024-05-17)

## 让我们来学习如何识别并处理机器学习模型中常见的数据泄露原因

[](https://ivopbernardo.medium.com/?source=post_page---byline--17113406f9f8--------------------------------)![Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--17113406f9f8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17113406f9f8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17113406f9f8--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--17113406f9f8--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17113406f9f8--------------------------------) ·阅读时间：8 分钟·2024 年 5 月 17 日

--

![](img/d8825e42f7b4f0a70e19b30eaddab533.png)

图片来自 [realaxer](https://unsplash.com/pt-br/@realaxer) @Unsplash.com

数据科学家最害怕的噩梦之一就是数据泄露。那为什么数据泄露如此有害呢？**因为数据泄露可能会通过一些方式让你认为模型非常优秀，并且你应该期待未来获得惊人的结果。**

如你所知，每个机器学习模型的主要目标是将其所学习到的模式推广到未来的数据中。由于我们无法真正访问未来的数据（除非先发明时间机器），因此在模型开发过程中模拟这一点的最佳方式是预留出测试集和验证集，用来评估模型性能。

在开发过程中，数据科学家的目标是提高模型在测试集和验证集上的表现。如果结果令人满意，数据科学家便会认为模型已准备好投入生产。这也是数据泄露如此危险的原因。因为它能够伪装成看似无害的行为，潜伏在后台，等待你将模型投入生产后，才发现现实中的表现远不如测试集中的表现。
