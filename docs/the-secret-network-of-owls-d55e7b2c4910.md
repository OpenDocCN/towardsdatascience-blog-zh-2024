# 猫头鹰的秘密网络

> 原文：[https://towardsdatascience.com/the-secret-network-of-owls-d55e7b2c4910?source=collection_archive---------0-----------------------#2024-08-04](https://towardsdatascience.com/the-secret-network-of-owls-d55e7b2c4910?source=collection_archive---------0-----------------------#2024-08-04)

![](../Images/b995ef4b844b2698a35dc08b69aefb65.png)

## 向国际猫头鹰意识日献上的数据致敬

[](https://medium.com/@janosovm?source=post_page---byline--d55e7b2c4910--------------------------------)[![Milan Janosov](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--d55e7b2c4910--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d55e7b2c4910--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d55e7b2c4910--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--d55e7b2c4910--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d55e7b2c4910--------------------------------) ·阅读时长 6 分钟 ·2024年8月4日

--

你知道8月4日是国际猫头鹰意识日吗？直到我无聊地浏览一些可爱的猫头鹰表情包，才发现这个[网站](https://www.daysoftheyear.com/days/international-owl-awareness-day/)。随后，因为我们最近在花园里发现了一个可爱的猫头鹰家庭，我决定在维基百科上查阅一下它们 — 结果让我惊讶的是，维基百科上竟然记录了[254种猫头鹰物种](https://en.wikipedia.org/wiki/List_of_owl_species)，这是我们最喜爱的免费开放知识来源。接下来的唯一合乎逻辑的步骤，就是将这些信息转化为数据可视化，更好地理解国际猫头鹰的分布情况。

具体来说，我将自动下载猫头鹰物种的完整列表，然后获取它们的维基百科简介。接着，我将使用文本匹配和 NetworkX 图形分析库提取猫头鹰物种的相似性网络，并进行可视化。通过这种方式，我们将得到猫头鹰物种的可视化表示，这将大大简化我们解读不同物种之间关系的过程。

此外，尽管这里的话题是猫头鹰，但这些方法和步骤同样适用于任何我们希望覆盖并转化为知识图谱的主题，依赖于公开可用的维基百科数据库。

*所有图片均由作者创作。*

# 1. 收集猫头鹰物种列表
