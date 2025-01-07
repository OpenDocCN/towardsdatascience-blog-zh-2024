# 预测德国太阳能生产：使用 Prophet 的实际方法

> 原文：[`towardsdatascience.com/forecasting-germanys-solar-energy-production-a-practical-approach-with-prophet-717aa23ecd58?source=collection_archive---------6-----------------------#2024-09-11`](https://towardsdatascience.com/forecasting-germanys-solar-energy-production-a-practical-approach-with-prophet-717aa23ecd58?source=collection_archive---------6-----------------------#2024-09-11)

## 使用 Python 的分析与实现

[](https://medium.com/@aashishnair?source=post_page---byline--717aa23ecd58--------------------------------)![Aashish Nair](https://medium.com/@aashishnair?source=post_page---byline--717aa23ecd58--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--717aa23ecd58--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--717aa23ecd58--------------------------------) [Aashish Nair](https://medium.com/@aashishnair?source=post_page---byline--717aa23ecd58--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--717aa23ecd58--------------------------------) ·阅读时长 8 分钟·2024 年 9 月 11 日

--

![](img/675c1de3e4e76758b58c250076ff23cd.png)

Pixabay 图片：[`www.pexels.com/photo/blue-solar-panel-board-356036/`](https://www.pexels.com/photo/blue-solar-panel-board-356036/)

## 目录

∘ 介绍

∘ 为什么要预测太阳能？

∘ 数据

∘ 探索性数据分析

∘ 为什么选择 Prophet？

∘ 模型评估标准

∘ 基准模型

∘ Prophet 模型（默认超参数）

∘ Prophet 模型（调整后的超参数）

∘ 结果与讨论

∘ 未来步骤

∘ 结论

∘ 参考文献

## 介绍

德国目前正在进行*Energiewende*，这是一项长期的能源转型，旨在实现碳中和，主要依靠可再生能源资源来发电。太阳能在确保德国能源安全方面发挥着关键作用。

因此，这一转型的成功在很大程度上依赖于准确预测未来太阳能产出的能力。本文探讨了使用 Prophet 库预测德国太阳能发电的可行性。
