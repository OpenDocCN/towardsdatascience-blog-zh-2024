# 从农场到餐桌：分类模型的工作流程

> 原文：[https://towardsdatascience.com/farm-to-table-the-workflow-of-a-classification-model-7abc41989767?source=collection_archive---------6-----------------------#2024-04-02](https://towardsdatascience.com/farm-to-table-the-workflow-of-a-classification-model-7abc41989767?source=collection_archive---------6-----------------------#2024-04-02)

## 比较逻辑回归与随机森林分类在食谱推荐中的应用

[](https://medium.com/@john_lenehan?source=post_page---byline--7abc41989767--------------------------------)[![John Lenehan](../Images/addeeb0bacca7ddec928aa12c2a4fc53.png)](https://medium.com/@john_lenehan?source=post_page---byline--7abc41989767--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7abc41989767--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7abc41989767--------------------------------) [John Lenehan](https://medium.com/@john_lenehan?source=post_page---byline--7abc41989767--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7abc41989767--------------------------------) ·阅读时间 17 分钟 ·2024年4月2日

--

![](../Images/f2289bad26609d87f7e1595937176047.png)

图片来自 [Zan](https://unsplash.com/@zanilic?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

一个典型的机器学习工作流程很少仅依靠单一的方法来解决当前问题。模型通常会经历一个迭代过程，应用各种技术并进行评估。特征工程策略会经过测试、丢弃，再次回顾；算法及其参数会经过彻底的迭代，有时只为了提升极小的百分比。这种实验和精炼的循环过程对于实现一个稳健的解决方案至关重要。

![](../Images/8f2ad306c2bca970724bb59f0ddb8d5b.png)

图片来自 [Muradi](https://unsplash.com/@simuradi?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

以下文章演示了在准备、测试、比较和评分分类模型时的典型工作流程。在这个例子中，一个假想的烹饪网站的产品团队正试图通过基于他们手动选择的食谱的过去表现来改进当前的前端食谱推荐系统。为此，应用了两种算法——一个逻辑回归……
