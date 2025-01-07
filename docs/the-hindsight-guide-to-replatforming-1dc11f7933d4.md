# 《平台重构的事后指南》

> 原文：[https://towardsdatascience.com/the-hindsight-guide-to-replatforming-1dc11f7933d4?source=collection_archive---------3-----------------------#2024-10-05](https://towardsdatascience.com/the-hindsight-guide-to-replatforming-1dc11f7933d4?source=collection_archive---------3-----------------------#2024-10-05)

## *剖析软件“心脏移植”的动机、风险和潜在回报*

[](https://medium.com/@ethan.m.knox?source=post_page---byline--1dc11f7933d4--------------------------------)[![Ethan Knox](../Images/a8e3a63ce5f093b3e015f058714c6255.png)](https://medium.com/@ethan.m.knox?source=post_page---byline--1dc11f7933d4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1dc11f7933d4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1dc11f7933d4--------------------------------) [Ethan Knox](https://medium.com/@ethan.m.knox?source=post_page---byline--1dc11f7933d4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1dc11f7933d4--------------------------------) ·阅读时间：16分钟·2024年10月5日

--

![](../Images/d6865eb3ebff9d0d061c5217d12f1597.png)

动力系统的演变 — 由 Midjourney v6.1 生成的 AI 图像

我正在为一个客户项目绘制架构建议，该项目在成功完成概念验证（POC）后，已经准备好进入生产软件的黄金阶段。我停下来问自己，*为什么*我所创建的新架构图与现有概念软件的架构看起来差异如此之大；即使我的答案似乎很有说服力（包括了可靠性、速度、规模和安全性的具体衡量标准），我还是摆脱不了一种不安的感觉，这种感觉似乎不太对劲。我开始回顾过去十五年中我直接负责（例如多个现代数据堆栈转换）或间接参与的重构项目（例如在“旧”系统和“新”系统之间的数据仓库协调）。结果我列出了十三个平台重构项目。在这十三个项目中，最终只有四个取得了积极的产品或工程成果；这意味着我亲历的重构项目中大约有**70%的失败率**。难怪每当涉及重写、重新架构或重新思考时，我都会立刻停下来深思。

平台重构是资源密集型、耗时的项目，可能决定高管的职业生涯，标志着一个组织增长的“曲棍球棒”拐点，或让组织陷入困境……
