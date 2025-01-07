# Jet Sweep：优化路线以访问每支NFL球队的主场

> 原文：[https://towardsdatascience.com/jet-sweep-route-optimization-to-visit-every-nfl-team-at-home-9a43e0bc4810?source=collection_archive---------3-----------------------#2024-06-03](https://towardsdatascience.com/jet-sweep-route-optimization-to-visit-every-nfl-team-at-home-9a43e0bc4810?source=collection_archive---------3-----------------------#2024-06-03)

## 用线性规划解决旅行商问题

[](https://sejaldua.medium.com/?source=post_page---byline--9a43e0bc4810--------------------------------)[![Sejal Dua](../Images/b9ec1f4907da5e6dfa1c922caa5b326d.png)](https://sejaldua.medium.com/?source=post_page---byline--9a43e0bc4810--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9a43e0bc4810--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9a43e0bc4810--------------------------------) [Sejal Dua](https://sejaldua.medium.com/?source=post_page---byline--9a43e0bc4810--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9a43e0bc4810--------------------------------) ·阅读时长10分钟·2024年6月3日

--

![](../Images/eabbecc402ec4c3700b9a4c9db95b52c.png)

图片来源：[Brendan Beale](https://unsplash.com/@theophilus318?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

大多数体育行业人士或狂热粉丝都曾想过，*“一生中能访问每一个NFL体育场、NBA体育馆或MLB棒球场不是很酷吗？”* 虽然从我现在的位置来看，这个目标似乎遥不可及，但我足够关注篮球、棒球和美式足球，时不时地会想起这个愿望清单上的项目。我最近还对各种调度和路线优化问题产生了浓厚的兴趣。因此，我为你呈现一个体育迷的梦想假期。随着2024–25赛季NFL赛程的发布，你正在计划一次旅行，旨在以最优路线访问每一个NFL体育场，参加主场比赛。

# NFL相关背景

从零开始解决这个问题时，我们需要获取一些初步信息：

(1) 每个NFL体育场的纬度和经度坐标。

(2) 即将到来的NFL赛程，包括日期和主场地点。

## 体育场详情与距离矩阵

我写了一个简单的Python脚本，从维基百科中获取每个NFL体育场的纬度和经度信息。
