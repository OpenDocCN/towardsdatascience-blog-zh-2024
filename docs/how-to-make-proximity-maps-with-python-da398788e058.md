# 如何使用 Python 制作邻近地图

> 原文：[https://towardsdatascience.com/how-to-make-proximity-maps-with-python-da398788e058?source=collection_archive---------4-----------------------#2024-10-30](https://towardsdatascience.com/how-to-make-proximity-maps-with-python-da398788e058?source=collection_archive---------4-----------------------#2024-10-30)

## 快速成功的数据科学

## Geopy 的大圆法则

[](https://medium.com/@lee_vaughan?source=post_page---byline--da398788e058--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--da398788e058--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--da398788e058--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--da398788e058--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--da398788e058--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da398788e058--------------------------------) ·11 分钟阅读·2024年10月30日

--

![](../Images/16a114e25cdb33cd1f8f95f8b61f0276.png)

来自密西西比州立大学的距离地图（作者提供）

你是否注意到社交媒体上有些“距离”地图？我刚看到一个由[Todd Jones](https://www.reddit.com/r/dataisbeautiful/comments/101mbsr/distance_to_the_nearest_national_park_oc/)制作的地图，展示了在美国48个州内的任何位置，你距离最近的国家公园有多远。

这些邻近地图既有趣又实用。如果你是一个生存主义者，可能会希望远离潜在的核导弹目标；如果你是一个狂热的钓鱼爱好者，可能会希望靠近一个[巴斯钓具店](https://www.reddit.com/r/dataisbeautiful/comments/102buf3/distance_to_the_nearest_bass_pro_shops_oc/#lightbox)。

我曾和一个几乎对美国大学橄榄球一无所知的英国人一起上研究生院。尽管如此，他在我们的每周赌注池中表现得非常好。他的一个秘密是：如果有球队需要旅行超过300英里，他就会下注反对这个球队，前提是两队水平相当，或者主队被看好。

在这个*快速成功的数据科学*项目中，我们将使用 Python 为东南联盟（SEC）的大学橄榄球队制作“距离”地图。我们将找出哪支队伍平均需要最长的旅行时间才能与其他队伍比赛，以及哪支队伍的旅行时间最短。然后，我们将在美国东南部的地图上标出这些距离的等高线。此外，我们还将探讨如何对其他连续数据（如温度）进行网格化和等高线绘制。
