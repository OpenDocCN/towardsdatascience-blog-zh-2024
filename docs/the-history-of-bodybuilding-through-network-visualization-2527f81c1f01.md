# 通过网络可视化展示健美的历史

> 原文：[https://towardsdatascience.com/the-history-of-bodybuilding-through-network-visualization-2527f81c1f01?source=collection_archive---------10-----------------------#2024-05-30](https://towardsdatascience.com/the-history-of-bodybuilding-through-network-visualization-2527f81c1f01?source=collection_archive---------10-----------------------#2024-05-30)

![](../Images/f086978a8f730cd7dbc5c05bf9658310.png)

## 使用 Python 和 Gephi 构建奥林匹亚先生获奖者共享领奖台图（1965–2023）。

[](https://medium.com/@janosovm?source=post_page---byline--2527f81c1f01--------------------------------)[![Milan Janosov](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--2527f81c1f01--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2527f81c1f01--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2527f81c1f01--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--2527f81c1f01--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2527f81c1f01--------------------------------) ·5分钟阅读·2024年5月30日

--

*除非标明来源，否则所有图片和代码由作者创建。*

我已经对举重充满热情约十年了，所以是时候绘制这项运动的伟大传奇，特别是由阿诺德·施瓦辛格所象征的竞技健美，使用数据驱动的方法。因此，我首先收集了每届奥林匹亚先生比赛前三名的位置数据，并创建了获胜者的共享领奖台网络。这个网络，后来在 Gephi 中可视化，应该能够突出不同的时代及其在这项运动中的关键人物。

此外，本文旨在说明如何使用数据科学和网络可视化，揭示任何社交生态系统中的隐藏连接，无论其规模大小，来自体育或艺术的世界，具有小或巨大商业价值。

# 1\. 数据准备

## **1.1\. 收集数据**

我通常从数据收集开始——比如编写爬虫或调用 API。然而，对于这个练习，解决方案异常简单……
