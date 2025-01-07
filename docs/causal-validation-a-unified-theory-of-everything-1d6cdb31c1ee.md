# 因果验证：统一的万物理论

> 原文：[https://towardsdatascience.com/causal-validation-a-unified-theory-of-everything-1d6cdb31c1ee?source=collection_archive---------3-----------------------#2024-05-16](https://towardsdatascience.com/causal-validation-a-unified-theory-of-everything-1d6cdb31c1ee?source=collection_archive---------3-----------------------#2024-05-16)

## 如何检测和修正有向无环图中的任何类型的错误，以确保它是对基础数据的有效表示

[](https://grahamharrison-86487.medium.com/?source=post_page---byline--1d6cdb31c1ee--------------------------------)[![Graham Harrison](../Images/c6bfe00c6e0cfcdf3bd042c7fdc03554.png)](https://grahamharrison-86487.medium.com/?source=post_page---byline--1d6cdb31c1ee--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1d6cdb31c1ee--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1d6cdb31c1ee--------------------------------) [Graham Harrison](https://grahamharrison-86487.medium.com/?source=post_page---byline--1d6cdb31c1ee--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1d6cdb31c1ee--------------------------------) ·28分钟阅读·2024年5月16日

--

![](../Images/336d0cc46fb3a37e6be559753c8d160e.png)

图片由[Guillermo Ferla](https://unsplash.com/@gferla?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/starry-night-sky-over-starry-night-lLExhYl-cXQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 引言

因果推断是机器学习中的一个新兴领域，它不仅能够预测可能发生的事情，还能解释为什么会发生，通过这样做，它有望永久解决潜在的问题，而不是应对可能的后果。

因果模型的一个关键组成部分是“有向无环图”（DAG），它以简单的可视化格式捕捉变量和事件之间的因果关系，但DAG的主要问题是它们通常由领域专家主观构建。

因此，DAG并不能保证是正确的，如果它不正确，则因果推断模型的计算和结论很可能会出错。

**因果验证**是用来描述将DAG与其所代表的基础数据进行对比检查的过程，目的是识别并修正其中的错误或不一致。如果这一过程可以可靠地完成，那么它将确保因果推断及相关结论的正确性。
