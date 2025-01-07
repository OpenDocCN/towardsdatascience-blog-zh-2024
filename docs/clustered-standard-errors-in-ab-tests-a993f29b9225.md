# A/B 测试中的聚类标准误

> 原文：[https://towardsdatascience.com/clustered-standard-errors-in-ab-tests-a993f29b9225?source=collection_archive---------7-----------------------#2024-03-17](https://towardsdatascience.com/clustered-standard-errors-in-ab-tests-a993f29b9225?source=collection_archive---------7-----------------------#2024-03-17)

## [因果数据科学](https://towardsdatascience.com/tagged/causal-data-science)

## *当观察单位与随机化单位不同该怎么办*

[](https://medium.com/@matteo.courthoud?source=post_page---byline--a993f29b9225--------------------------------)[![Matteo Courthoud](../Images/d873eab35a0cf9fc696658c0bee16b33.png)](https://medium.com/@matteo.courthoud?source=post_page---byline--a993f29b9225--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a993f29b9225--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a993f29b9225--------------------------------) [Matteo Courthoud](https://medium.com/@matteo.courthoud?source=post_page---byline--a993f29b9225--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a993f29b9225--------------------------------) ·阅读时间 13 分钟·2024年3月17日

--

![](../Images/be0586977014aaf6a6483c247d8cf50c.png)

封面，图片来自作者

A/B 测试是因果推断的黄金标准，因为它们通过**随机化**使我们能够在最小的假设下做出有效的因果声明。事实上，通过随机分配**处理**（如药物、广告、产品等），我们能够比较不同**对象**（如病人、用户、顾客等）之间的**结果**（如疾病、公司收入、客户满意度等），并将结果的平均差异归因于处理的因果效应。

有时候会出现**处理分配的单位与观察单位不同**的情况。换句话说，我们并不是独立地对每个观察单位进行是否处理的决策，而是以群体为单位进行处理。例如，我们可能决定对某个地区的所有客户进行处理，同时在客户层面观察结果，或者对某个品牌的所有商品进行处理，而在商品层面观察结果。通常这种情况是由于实际约束所致。在第一个例子中，所谓的*地理实验*，因为由于 cookie 的弃用，我们无法追踪用户。
