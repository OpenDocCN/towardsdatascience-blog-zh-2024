# 地理编码与艺术的交集：利用开放街图和Networkx

> 原文：[https://towardsdatascience.com/the-intersection-of-geocoding-and-art-with-open-street-map-and-networkx-d3cff5a56200?source=collection_archive---------7-----------------------#2024-01-16](https://towardsdatascience.com/the-intersection-of-geocoding-and-art-with-open-street-map-and-networkx-d3cff5a56200?source=collection_archive---------7-----------------------#2024-01-16)

## 使用 Python 绘图和网络指标分析城市街道地图

[](https://sejaldua.medium.com/?source=post_page---byline--d3cff5a56200--------------------------------)[![Sejal Dua](../Images/b9ec1f4907da5e6dfa1c922caa5b326d.png)](https://sejaldua.medium.com/?source=post_page---byline--d3cff5a56200--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d3cff5a56200--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d3cff5a56200--------------------------------) [Sejal Dua](https://sejaldua.medium.com/?source=post_page---byline--d3cff5a56200--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3cff5a56200--------------------------------) ·8 分钟阅读·2024年1月16日

--

![](../Images/3382145714d93a1928f643d6c16e938e.png)

图片由 [Logan Armstrong](https://unsplash.com/@loganstrongarms?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我可以花上好几个小时，试图说服一群人，为什么我的城市（纽约）是世界上最好的城市。然而，我最近意识到，我们对城市的看法是根据我们自身的评估标准而变化的。城市可以从多个方面进行评估，包括天气、食物、夜生活、景点、步行便利性、自行车便利性、公共交通质量、体育队伍、接触大自然的机会等。

在最近与一些同事和朋友的讨论中，有人表示交通便利性应该比天气更加重要，而其他人则反驳说，只要有足够多的活动可以做，他们就能忍受刺骨的寒风。对城市进行排名或分层是一个高度主观的活动，部分原因在于应考虑的因素模糊不清，但也因为城市可以从不同的背景下进行评估：是为了居住，还是为了周末旅行，或是为了度过一周的假期。就个人而言，尽管我一生中曾在四座城市居住过，但我发现随着我的优先事项变化，我自己对城市的排名也会略有变化。例如，我会更高评价波士顿作为居住地，而不是作为旅游地，而我对迈阿密的评价则…
