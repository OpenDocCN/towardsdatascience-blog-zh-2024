# 你应该注意的两个隐秘的 PySpark 错误

> 原文：[https://towardsdatascience.com/2-silent-pyspark-mistakes-you-should-be-aware-of-de52c3a188c4?source=collection_archive---------14-----------------------#2024-02-16](https://towardsdatascience.com/2-silent-pyspark-mistakes-you-should-be-aware-of-de52c3a188c4?source=collection_archive---------14-----------------------#2024-02-16)

## 在处理大型数据集时，小错误可能会导致严重后果。

[](https://sonery.medium.com/?source=post_page---byline--de52c3a188c4--------------------------------)[![Soner Yıldırım](../Images/c589572e9d1ee176cd4f5a0008173f1b.png)](https://sonery.medium.com/?source=post_page---byline--de52c3a188c4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--de52c3a188c4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--de52c3a188c4--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--de52c3a188c4--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--de52c3a188c4--------------------------------) ·阅读时间：5分钟·2024年2月16日

--

![](../Images/e6d67208592c34319ca46e35f9a5143c.png)

图片来源：[Ernie A. Stephens](https://unsplash.com/@eas071?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 通过 [Unsplash](https://unsplash.com/photos/stack-of-stack-of-books-KflQqYcFknk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在编程中，当我们犯错时，并不总是会报错。代码能够正常工作，不会抛出异常，我们以为一切都没问题。那些不会导致脚本失败的错误，很难被发现和调试。

在数据科学中，发现这些错误更具挑战性，因为我们通常不会得到一个单一的输出结果。

假设我们有一个包含数百万行的数据集。我们在计算销售数量时犯了一个错误。然后，我们基于销售数量创建了聚合特征，如每周总数、过去14天的移动平均等。这些特征被用于一个机器学习模型，该模型预测下一周的需求。

我们评估预测结果，发现准确率不够好。然后，我们花了很多时间尝试不同的方法来提高准确率，比如特征工程或超参数调优。这些策略对准确率的影响不大，因为问题出在数据上。

这是我们在处理大型数据集时可能遇到的一个场景。在本文中，我们将讨论两个可能导致意外结果的 PySpark 错误。
