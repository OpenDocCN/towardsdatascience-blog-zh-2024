# FinalMLP：一种简单而强大的双流 MLP 模型，用于推荐系统

> 原文：[`towardsdatascience.com/finalmlp-a-simple-yet-powerful-two-stream-mlp-model-for-recommendation-systems-fc21f3e3fb3d?source=collection_archive---------10-----------------------#2024-02-24`](https://towardsdatascience.com/finalmlp-a-simple-yet-powerful-two-stream-mlp-model-for-recommendation-systems-fc21f3e3fb3d?source=collection_archive---------10-----------------------#2024-02-24)

## 探索 FinalMLP 如何改变在线推荐：利用前沿 AI 研究解锁个性化体验

[](https://medium.com/@luisroque?source=post_page---byline--fc21f3e3fb3d--------------------------------)![Luís Roque](https://medium.com/@luisroque?source=post_page---byline--fc21f3e3fb3d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc21f3e3fb3d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc21f3e3fb3d--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--fc21f3e3fb3d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc21f3e3fb3d--------------------------------) ·13 分钟阅读·2024 年 2 月 24 日

--

*本文由 Rafael Guedes 共同撰写。*

# 引言

世界正在向数字化时代发展，每个人几乎可以通过轻点鼠标获得他们想要的一切。这种便捷性、舒适性和大量选择的优势，同时也给消费者带来了新的挑战。我们如何帮助他们做出个性化的选择，而不是在海量的选项中进行搜索呢？这就是推荐系统的作用所在。

推荐系统对于组织而言，能有效增加交叉销售、推动长尾商品的销售，并通过分析客户的偏好来提升决策制定。更重要的是，它们能够通过学习客户的历史行为，在给定一组产品时，根据特定客户的偏好进行排序。使用推荐系统的组织比竞争对手更具优势，因为它们提供了更好的客户体验。

本文聚焦于 FinalMLP，这是一种旨在提升在线广告和推荐系统中点击率（CTR）预测的新模型。通过集成两个多层感知器（MLP）网络，并结合门控和交互聚合层等先进特性…
