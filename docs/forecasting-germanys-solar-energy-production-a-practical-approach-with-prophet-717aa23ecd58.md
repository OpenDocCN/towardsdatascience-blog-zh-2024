# 预测德国太阳能生产：使用Prophet的实际方法

> 原文：[https://towardsdatascience.com/forecasting-germanys-solar-energy-production-a-practical-approach-with-prophet-717aa23ecd58?source=collection_archive---------6-----------------------#2024-09-11](https://towardsdatascience.com/forecasting-germanys-solar-energy-production-a-practical-approach-with-prophet-717aa23ecd58?source=collection_archive---------6-----------------------#2024-09-11)

## 使用Python的分析与实现

[](https://medium.com/@aashishnair?source=post_page---byline--717aa23ecd58--------------------------------)[![Aashish Nair](../Images/23f4b3839e464419332b690a4098d824.png)](https://medium.com/@aashishnair?source=post_page---byline--717aa23ecd58--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--717aa23ecd58--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--717aa23ecd58--------------------------------) [Aashish Nair](https://medium.com/@aashishnair?source=post_page---byline--717aa23ecd58--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--717aa23ecd58--------------------------------) ·阅读时长8分钟·2024年9月11日

--

![](../Images/675c1de3e4e76758b58c250076ff23cd.png)

Pixabay图片：[https://www.pexels.com/photo/blue-solar-panel-board-356036/](https://www.pexels.com/photo/blue-solar-panel-board-356036/)

## 目录

∘ [介绍](#fee5)

∘ [为什么要预测太阳能？](#fcb3)

∘ [数据](#a883)

∘ [探索性数据分析](#a4e6)

∘ [为什么选择Prophet？](#a83f)

∘ [模型评估标准](#376e)

∘ [基准模型](#43af)

∘ [Prophet模型（默认超参数）](#2ff4)

∘ [Prophet模型（调整后的超参数）](#9626)

∘ [结果与讨论](#234a)

∘ [未来步骤](#8cc2)

∘ [结论](#ec8f)

∘ [参考文献](#624b)

## 介绍

德国目前正在进行*Energiewende*，这是一项长期的能源转型，旨在实现碳中和，主要依靠可再生能源资源来发电。太阳能在确保德国能源安全方面发挥着关键作用。

因此，这一转型的成功在很大程度上依赖于准确预测未来太阳能产出的能力。本文探讨了使用Prophet库预测德国太阳能发电的可行性。
