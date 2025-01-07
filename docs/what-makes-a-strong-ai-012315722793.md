# 什么造就了一个强大的人工智能？

> 原文：[https://towardsdatascience.com/what-makes-a-strong-ai-012315722793?source=collection_archive---------18-----------------------#2024-01-04](https://towardsdatascience.com/what-makes-a-strong-ai-012315722793?source=collection_archive---------18-----------------------#2024-01-04)

![](../Images/662e6e2de5ba10fba90150cf93ca5680.png)

图片由[Emmanuel Ikwuegbu](https://unsplash.com/@emmages?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## [《为什么之书》](https://amzn.to/4grzPgC)第9章和第10章，与我一起阅读系列

[](https://zzhu17.medium.com/?source=post_page---byline--012315722793--------------------------------)[![Zijing Zhu, PhD](../Images/436b22e28798b87261c4814a7e2b20e3.png)](https://zzhu17.medium.com/?source=post_page---byline--012315722793--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--012315722793--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--012315722793--------------------------------) [Zijing Zhu, PhD](https://zzhu17.medium.com/?source=post_page---byline--012315722793--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--012315722793--------------------------------) ·阅读时长13分钟·2024年1月4日

--

我们正在阅读最后一篇文章，书中探讨了Judea Pearl启发性的《为什么之书》([The Book of Why](https://amzn.to/4grzPgC))，主要聚焦于中介变量以及如何将因果关系与大数据结合以得出可靠的结论。我还将根据整本书的内容总结什么造就了一个强大的人工智能。

## 中介变量

在之前的文章中，我们简要讨论了中介效应。我们提到，识别中介变量是非常重要的，这不仅是因为我们不应该将其视为混杂因子，因为对它们进行条件化会完全或部分阻断因果效应，而且在适当的情况下，当存在未观察到的混杂因子时，我们可以利用中介变量来应用前门准则。

![](../Images/d1b2b598ee6e3fccef04dbd706fe1f96.png)

图片由[sally crierie](https://unsplash.com/@sallycrierie?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在本节中，我们将深入探讨不同的中介变量实例，并理解在中介变量存在的情况下，如何正确定义直接因果效应、间接因果效应和总因果效应。

在历史上，坏血病一直是水手们最恐惧的疾病。在维生素被发现并被正确命名之前，科学家们一直在努力弄清楚如何防止水手们患上这种疾病……
