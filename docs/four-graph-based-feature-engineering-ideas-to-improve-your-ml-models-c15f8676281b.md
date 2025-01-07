# 四个基于图的特征工程思路，提升你的机器学习模型表现

> 原文：[https://towardsdatascience.com/four-graph-based-feature-engineering-ideas-to-improve-your-ml-models-c15f8676281b?source=collection_archive---------1-----------------------#2024-03-18](https://towardsdatascience.com/four-graph-based-feature-engineering-ideas-to-improve-your-ml-models-c15f8676281b?source=collection_archive---------1-----------------------#2024-03-18)

## 探索使用Python中networkx的创新图形特征工程技术，揭示表格数据中的隐藏洞察

[](https://ds-claudia.medium.com/?source=post_page---byline--c15f8676281b--------------------------------)[![Claudia Ng](../Images/81d7927943f38f6303690cfd5676c8fd.png)](https://ds-claudia.medium.com/?source=post_page---byline--c15f8676281b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c15f8676281b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c15f8676281b--------------------------------) [Claudia Ng](https://ds-claudia.medium.com/?source=post_page---byline--c15f8676281b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c15f8676281b--------------------------------) ·11分钟阅读·2024年3月18日

--

# 介绍

想要提升你的机器学习模型性能吗？考虑花更多时间进行特征工程。

现实世界中许多数据类型实际上是不同实体之间的关系，但这些关系在表格数据中很难捕捉。本文将介绍四种基于图的特征工程思路，用于优化你的机器学习模型。

本文中的示例将主要使用[networkx](https://networkx.org/)来进行基于图的特征工程，因此如果你想跟着一起操作，确保在你的虚拟环境中使用`pip install networkx`进行安装。让我们开始吧！

# 基于图的特征有哪些应用场景？

一些可以使用基于图的特征的例子包括：

+   社交网络：用于捕捉账户之间关系以及检测账户社区的特征；

+   推荐系统：捕捉用户与物品之间交互的特征；

+   财务欺诈：捕捉用户与商户之间交易的特征；

+   交通预测：用于…
