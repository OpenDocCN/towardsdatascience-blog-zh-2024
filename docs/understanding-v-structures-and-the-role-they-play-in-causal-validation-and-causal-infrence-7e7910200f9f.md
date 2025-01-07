# 理解V结构及其在因果验证和因果推断中发挥的关键作用

> 原文：[https://towardsdatascience.com/understanding-v-structures-and-the-role-they-play-in-causal-validation-and-causal-infrence-7e7910200f9f?source=collection_archive---------7-----------------------#2024-03-19](https://towardsdatascience.com/understanding-v-structures-and-the-role-they-play-in-causal-validation-and-causal-infrence-7e7910200f9f?source=collection_archive---------7-----------------------#2024-03-19)

## 如何检测并纠正有向无环图中与基础数据不符的因果关系方向

[](https://grahamharrison-86487.medium.com/?source=post_page---byline--7e7910200f9f--------------------------------)[![Graham Harrison](../Images/c6bfe00c6e0cfcdf3bd042c7fdc03554.png)](https://grahamharrison-86487.medium.com/?source=post_page---byline--7e7910200f9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7e7910200f9f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7e7910200f9f--------------------------------) [Graham Harrison](https://grahamharrison-86487.medium.com/?source=post_page---byline--7e7910200f9f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e7910200f9f--------------------------------) ·阅读时长27分钟·2024年3月19日

--

![](../Images/fd736d962b63402b69b1f3e2bbb62efc.png)

图片由[Fré Sonneveld](https://unsplash.com/@fresonneveld?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/black-transmission-towers-under-green-sky-q6n8nIrDQHE?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

因果推断是机器学习中的一个新兴领域，它不仅能够预测可能发生的事情，还能解释为什么会发生，并且通过这样做，有望永久解决根本问题，而不是仅仅应对潜在的后果。

解决因果推断问题需要通过“有向无环图”（Directed Acyclic Graph，简称DAG）可视化因果关系因素，该图通常由领域专家开发，他们对系统或过程中的因果关系有深入的了解。

这种方法面临的挑战是领域专家的观点可能存在缺陷或偏见，如果没有准确的DAG，因果模型的结果和输出将不准确，从而无效。因此，确保DAG准确反映因果关系的过程被称为因果验证。

因果验证中的一个具体问题是检测两个因素之间的因果方向…
