# 使用VLM和LLM实现常识抓取

> 原文：[https://towardsdatascience.com/grasping-with-common-sense-bfe21743c02d?source=collection_archive---------9-----------------------#2024-03-29](https://towardsdatascience.com/grasping-with-common-sense-bfe21743c02d?source=collection_archive---------9-----------------------#2024-03-29)

## 如何利用大型语言模型进行机器人抓取和代码生成

[](https://medium.com/@nikolaus.correll?source=post_page---byline--bfe21743c02d--------------------------------)[![Nikolaus Correll](../Images/948c44fe797b8057e20b39023c30027b.png)](https://medium.com/@nikolaus.correll?source=post_page---byline--bfe21743c02d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bfe21743c02d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bfe21743c02d--------------------------------) [Nikolaus Correll](https://medium.com/@nikolaus.correll?source=post_page---byline--bfe21743c02d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bfe21743c02d--------------------------------) ·6分钟阅读·2024年3月29日

--

抓取和操控仍然是机器人技术中的一个难题，尚未解决。抓取不仅仅是找出将手指放置在物体上的位置，以产生足够的约束力。抓取还涉及施加恰到好处的力量，既能捡起物体又不至于破坏它，同时确保物体能够按预期使用。同时，抓取还为检测物体的类型和属性提供了关键的传感器输入。随着移动性基本解决，抓取和操控仍然是实现真正自主劳动替代的最后一个前沿。

*如果你不是Medium的订阅者，可以* [*在这里*](https://medium.com/towards-data-science/grasping-with-common-sense-bfe21743c02d?sk=460bc3168cc4f5f2395b19b9a1d1c6bc) *免费阅读这篇文章*。

想象一下，你正在派遣你的[类人机器人](/are-the-humanoids-here-to-stay-050da171530b)去超市，并告诉它：“*检查鳄梨的成熟度，今天为做鳄梨酱拿一个鳄梨*”。 这里有很多事情在发生：

1.  “成熟度”这一品质并不是像草莓或西红柿那样通过颜色显而易见，而是需要*触觉*信息来判断。

1.  “抓一个鳄梨”，特别是一个成熟的鳄梨，意味着某种…
