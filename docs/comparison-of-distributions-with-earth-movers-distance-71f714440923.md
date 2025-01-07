# 使用地球搬运工距离比较分布

> 原文：[https://towardsdatascience.com/comparison-of-distributions-with-earth-movers-distance-71f714440923?source=collection_archive---------11-----------------------#2024-02-13](https://towardsdatascience.com/comparison-of-distributions-with-earth-movers-distance-71f714440923?source=collection_archive---------11-----------------------#2024-02-13)

## 通过理论和从零开始的计算理解EMD

[](https://medium.com/@jarom.hulet?source=post_page---byline--71f714440923--------------------------------)[![Jarom Hulet](../Images/0fdeb1a2df90cccdd8f2f4b84d5e54eb.png)](https://medium.com/@jarom.hulet?source=post_page---byline--71f714440923--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--71f714440923--------------------------------)[![数据科学之路](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--71f714440923--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--71f714440923--------------------------------)

·发表于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--71f714440923--------------------------------) ·阅读时间11分钟·2024年2月13日

--

![](../Images/a17e6c2e648dc74d7eefcf14ffebd8ac.png)

图片来源：Pexels.com，作者Alfo Medeiros

阅读完本文后，你将深入了解地球搬运工距离（也叫EMD或Wasserstein距离）的计算方法。通过这些知识，你会对它在各种应用中的优缺点有一个清晰的了解。

**内容**

1.  地球搬运工距离（EMD）的定义与直觉

1.  EMD的应用

1.  从零开始计算EMD

1.  使用scipy包计算EMD

1.  结论

**地球搬运工距离（EMD）的定义与直觉**

地球搬运工距离是一种用来衡量两个分布之间差异的具体计算方法。这个名字来源于它的直观解释。假设你有两堆土（或*泥土*），它们位于不同的位置并且形状不同。EMD就是要将第二堆土移动到与第一堆土相同的形状时所需要的工作量（定义为移动的*泥土*总量乘以距离）。

我认为通过一个例子来说明会更清楚：假设我们有两个分布A和B，我们想知道它们之间有多大的差异……
