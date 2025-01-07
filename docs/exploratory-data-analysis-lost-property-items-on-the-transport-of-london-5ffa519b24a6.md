# 探索性数据分析：伦敦交通中的失物招领

> 原文：[https://towardsdatascience.com/exploratory-data-analysis-lost-property-items-on-the-transport-of-london-5ffa519b24a6?source=collection_archive---------6-----------------------#2024-04-12](https://towardsdatascience.com/exploratory-data-analysis-lost-property-items-on-the-transport-of-london-5ffa519b24a6?source=collection_archive---------6-----------------------#2024-04-12)

## 使用Python、Pandas和Plotly获得统计洞察

[](https://dmitryelj.medium.com/?source=post_page---byline--5ffa519b24a6--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--5ffa519b24a6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ffa519b24a6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5ffa519b24a6--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--5ffa519b24a6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ffa519b24a6--------------------------------) ·9分钟阅读·2024年4月12日

--

![](../Images/2801acebdb8128f1d041cb0cb74d3e63.png)

伦敦地铁，图源：作者

如读者可能猜到的，这个故事有一个微不足道的开头：我把包忘在了公交车上。五分钟后，我意识到包丢了，但公交车已经开走了。回到家后，我查阅了公交公司网站，看是否可以申请失物认领，几天后，我幸运地找回了它。我住在阿姆斯特丹，这里的公共交通与[iLost](https://ilost.co/nl/org/connexxion)公司有合作，乘客可以通过该平台认领失物。这个网站的结构相当清晰，甚至无需注册就能查看不同人丢失的物品（个人信息显然是被隐藏的）。我本身有一种数据导向的思维方式，于是灵光一现——从文化人类学的角度来看，这类数据非常有价值，我们可以了解到公共交通和其他地方可能丢失的各种物品。可惜的是，iLost的许可协议不允许在未获得书面同意的情况下使用这些数据，且我的问题并没有得到回应。但我依然保持这个想法，开始在线寻找其他可用的资源，结果发现：

+   伦敦交通局（[TfL](https://tfl.gov.uk)）也提供了一个很好的失物招领服务。
