# 皮埃尔-西蒙·拉普拉斯、逆概率和中心极限定理

> 原文：[https://towardsdatascience.com/pierre-simon-laplace-inverse-probability-and-the-central-limit-theorem-d52bec2e0dba?source=collection_archive---------3-----------------------#2024-03-05](https://towardsdatascience.com/pierre-simon-laplace-inverse-probability-and-the-central-limit-theorem-d52bec2e0dba?source=collection_archive---------3-----------------------#2024-03-05)

![](../Images/4267fd56954984b2a5c96c662b4e0b47.png)

[CC BY-SA 4.0](https://commons.wikimedia.org/wiki/File:Pierre-Simon_de_Laplace_by_Johann_Ernst_Heinsius_(1775).jpg)/[公共领域](https://commons.wikimedia.org/wiki/File:Octobre_1793,_supplice_de_9_%C3%A9migr%C3%A9s.jpg)/[公共领域](https://archive.org/details/thorieanalytiqu01laplgoog/page/n6/mode/2up)/图片由作者提供[/公共领域](https://commons.wikimedia.org/wiki/File:Jean_Auguste_Dominique_Ingres,_Portrait_de_Napol%C3%A9on_Bonaparte_en_premier_consul.jpg)

## 关于拉普拉斯对逆概率的精彩解答及其中心极限定理的发现

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--d52bec2e0dba--------------------------------)[![Sachin Date](../Images/bd023298b414caf88f79b00ef032d065.png)](https://timeseriesreasoning.medium.com/?source=post_page---byline--d52bec2e0dba--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d52bec2e0dba--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d52bec2e0dba--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--d52bec2e0dba--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d52bec2e0dba--------------------------------) ·27分钟阅读·2024年3月5日

--

在1600年代末，[**雅各布·伯努利**](https://medium.com/towards-data-science/on-jacob-bernoulli-the-law-of-large-numbers-and-the-origins-of-the-central-limit-theorem-8101bc6104e1)思考了一个有趣的问题：如何估计一个样本空间无法完全访问的事件的概率？比如，如何估计在一生中被天上的闪电击中的概率？或者，如果你偏好一个不那么戏剧化的情境，如何估计一个充满未知数量黑白票的抽屉中黑票的真实比例？这个实验情境显然是二项式的：一定数量的独立试验，每次试验只有两种可能结果之一。雅各布·伯努利关于这个二项式实验的[沉思](https://dh-abstracts.library.virginia.edu/works/10061)使他发现了[**大数法则（弱法则）**](https://medium.com/towards-data-science/on-jacob-bernoulli-the-law-of-large-numbers-and-the-origins-of-the-central-limit-theorem-8101bc6104e1)。那大约是在1689年。

1733年，在伯努利发现大数法则（WLLN）后的四十年，一位名叫[**亚伯拉罕·德·莫伊弗**](https://medium.com/towards-data-science/abraham-de-moivre-his-famous-theorem-and-the-birth-of-the-normal-curve-ee11ab5f9f20)的杰出法国人，在英格兰过着捉襟见肘的流亡生活，他弄清楚了如何准确计算伯努利二项式思想实验中表达的概率。德·莫伊弗的方法被称为**德·莫伊弗定理**（[详见这里](https://medium.com/towards-data-science/abraham-de-moivre-his-famous-theorem-and-the-birth-of-the-normal-curve-ee11ab5f9f20)），成为18世纪和19世纪最具影响力的发现之一……
