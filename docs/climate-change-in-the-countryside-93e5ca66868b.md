# 乡村的气候变化

> 原文：[https://towardsdatascience.com/climate-change-in-the-countryside-93e5ca66868b?source=collection_archive---------7-----------------------#2024-08-28](https://towardsdatascience.com/climate-change-in-the-countryside-93e5ca66868b?source=collection_archive---------7-----------------------#2024-08-28)

## 快速成功的数据科学

## 气候战士的Python项目

[](https://medium.com/@lee_vaughan?source=post_page---byline--93e5ca66868b--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--93e5ca66868b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--93e5ca66868b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--93e5ca66868b--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--93e5ca66868b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--93e5ca66868b--------------------------------) ·阅读时长11分钟·2024年8月28日

--

![](../Images/3ac67dd6bf0dcc6c31aa82e086deb552.png)

DALL-E3展示的城市和乡村景观

我有一个亲戚，他认为温度上升只是“热岛效应”。也就是说，快速增长的钢铁水泥城市比绿色乡村地区加热得更快，保持热量的时间也更长。他说：“如果你只关注在较凉的乡村地区进行的温度测量，全球变暖的现象就会消失很多。” 

数据科学的一个好处是你可以成为自己的事实检查员。在这个*快速成功的数据科学*项目中，我们将使用来自德克萨斯州的温度数据来验证之前的假设。

# 什么是热岛？

根据[美国环境保护局](https://www.epa.gov/heatislands)的数据，热岛效应指的是城市化区域的温度比周围乡村地区高。人工建筑物，如楼房和道路，比自然景观（如森林和湖泊）吸收和重新释放更多的太阳热量。人类活动，包括开车和给建筑物降温，会产生额外的热量。在大城市中，由于这些建筑物和活动高度集中，相比周围乡村地区，会形成“热岛”现象，导致温度更高。
