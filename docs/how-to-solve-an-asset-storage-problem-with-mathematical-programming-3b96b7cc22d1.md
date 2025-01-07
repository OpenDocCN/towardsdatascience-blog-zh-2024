# 如何通过数学编程解决资产存储问题

> 原文：[https://towardsdatascience.com/how-to-solve-an-asset-storage-problem-with-mathematical-programming-3b96b7cc22d1?source=collection_archive---------5-----------------------#2024-07-11](https://towardsdatascience.com/how-to-solve-an-asset-storage-problem-with-mathematical-programming-3b96b7cc22d1?source=collection_archive---------5-----------------------#2024-07-11)

## 使用 Python 和 Gurobipy 解决二维摆放问题

[](https://medium.com/@luisfernandopa1212?source=post_page---byline--3b96b7cc22d1--------------------------------)[![Luis Fernando PÉREZ ARMAS, Ph.D.](../Images/ca1b4775c2b278769c38542ea989ac92.png)](https://medium.com/@luisfernandopa1212?source=post_page---byline--3b96b7cc22d1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b96b7cc22d1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3b96b7cc22d1--------------------------------) [Luis Fernando PÉREZ ARMAS, Ph.D.](https://medium.com/@luisfernandopa1212?source=post_page---byline--3b96b7cc22d1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b96b7cc22d1--------------------------------) ·阅读时间 18 分钟·2024年7月11日

--

![](../Images/c5da33a5f9f7c000ce70ce2bb5393c83.png)

搬家很难（图片由DALLE-3生成）

搬家很难，说实话，非常烦人——真的是让人受够了。尤其是当你没有新地方可以去时，搬家就变得更加具有挑战性。在这种情况下，你需要为自己的物品租一个临时存储地方，而你自己则暂时寄宿在朋友家，这样会非常昂贵，因为存储地方通常是按使用的面积收费（在你告诉我这种情况从未发生过之前，请允许我指出，这种情况正是我的一个朋友在从法国搬到波兰时经历的）。

如果搬家对个人来说已经很困难，那么试想搬迁并临时存储整个工厂的复杂性。你可能会想，“哇，Luis，这也太疯狂了吧，”但这种情况在现实生活中确实发生过。它曾经发生在我身上，遗憾的是，当时我没有足够的分析工具来应对它。

我曾在石油和天然气服务行业工作。在我最后几年里，国家的商业环境急剧恶化并变得极其不稳定。对于某些服务来说，情况变得无法承受，导致管理层决定止损并关闭这些服务。这些服务的一项重要成本就是它们运营设施的租金，所以……
