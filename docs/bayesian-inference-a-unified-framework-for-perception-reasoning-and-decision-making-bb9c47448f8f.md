# 贝叶斯推断：感知、推理与决策的统一框架

> 原文：[`towardsdatascience.com/bayesian-inference-a-unified-framework-for-perception-reasoning-and-decision-making-bb9c47448f8f?source=collection_archive---------3-----------------------#2024-01-04`](https://towardsdatascience.com/bayesian-inference-a-unified-framework-for-perception-reasoning-and-decision-making-bb9c47448f8f?source=collection_archive---------3-----------------------#2024-01-04)

[](https://jshen9889.medium.com/?source=post_page---byline--bb9c47448f8f--------------------------------)![Stephanie Shen](https://jshen9889.medium.com/?source=post_page---byline--bb9c47448f8f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bb9c47448f8f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bb9c47448f8f--------------------------------) [Stephanie Shen](https://jshen9889.medium.com/?source=post_page---byline--bb9c47448f8f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bb9c47448f8f--------------------------------) ·14 分钟阅读·2024 年 1 月 4 日

--

![](img/d9fd9dbd2a7d7e352877a0f27847dd27.png)

由[🇸🇮 Janko Ferlič](https://unsplash.com/@itfeelslikefilm?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，[在 Unsplash](https://unsplash.com/photos/photo-of-library-with-turned-on-lights-sfL_QOnmy00?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

> *“…生活中最重要的问题……事实上，大多数都是概率性问题。严格来说，几乎所有的知识都是有可能的。”*
> 
> *— 皮埃尔-西蒙·拉普拉斯，《概率哲学论文》*

两百多年前，法国数学家皮埃尔-西蒙·拉普拉斯意识到我们所面临的大多数问题本质上是概率性的，而我们的大部分知识也是基于概率而非绝对确定性的。在这个前提下，他充分发展了贝叶斯定理，这是概率论中的一个基本理论，尽管他并不知道，六十年前，英国牧师托马斯·贝叶斯（同样是统计学家和哲学家）已经描述了这一定理。因此，尽管拉普拉斯完成了大部分数学工作，贝叶斯定理最终还是以贝叶斯的名字命名。

与其悠久的历史相比，贝叶斯定理直到近几十年才引起广泛关注，并在各个学科中得到了显著应用，越来越多的人意识到该定理与我们的感知和认知过程更加契合。它体现了概率的动态调整，既受到新数据的影响，也受到已有知识的影响。此外，它解释了我们思维过程中反复迭代和发展的性质。
