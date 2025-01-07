# 置信区间与预测区间

> 原文：[https://towardsdatascience.com/confidence-interval-vs-prediction-interval-a6b0c4816a92?source=collection_archive---------3-----------------------#2024-11-24](https://towardsdatascience.com/confidence-interval-vs-prediction-interval-a6b0c4816a92?source=collection_archive---------3-----------------------#2024-11-24)

## 你应该了解的一个小而重要的区别

[](https://medium.com/@jodancker?source=post_page---byline--a6b0c4816a92--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--a6b0c4816a92--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6b0c4816a92--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a6b0c4816a92--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--a6b0c4816a92--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6b0c4816a92--------------------------------) ·阅读时间8分钟·2024年11月24日

--

![](../Images/df0c9fd55a65ea05e0b068ecfe262636.png)

（图片来自作者）

在许多与数据科学相关的任务中，我们希望知道我们对结果有多确定。知道我们能多大程度上信任结果有助于我们做出更好的决策。

一旦我们量化了结果的不确定性程度，我们可以将其用于：

+   情景规划，用于评估最佳情况和最差情况的情景

+   风险评估，评估对决策的影响

+   模型评估，比较不同模型及其性能

+   与决策者沟通，告知他们应该多大程度上相信结果

[](/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=post_page-----a6b0c4816a92--------------------------------) [## 不确定性量化及其重要性

### 如何通过三行代码提升你的机器学习模型

towardsdatascience.com](/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=post_page-----a6b0c4816a92--------------------------------)

## 不确定性来自哪里？

让我们来看一个简单的例子。我们想要估算德国一套300平方米房子的平均价格。收集所有300平方米房子的价格数据是不现实的。相反，我们将根据一个具有代表性的子集来计算平均价格。
