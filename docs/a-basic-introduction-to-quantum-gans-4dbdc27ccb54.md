# 量子生成对抗网络基础介绍

> 原文：[https://towardsdatascience.com/a-basic-introduction-to-quantum-gans-4dbdc27ccb54?source=collection_archive---------7-----------------------#2024-09-21](https://towardsdatascience.com/a-basic-introduction-to-quantum-gans-4dbdc27ccb54?source=collection_archive---------7-----------------------#2024-09-21)

## 一种量子-经典混合方法用于合成数据生成

[](https://javier-marin.medium.com/?source=post_page---byline--4dbdc27ccb54--------------------------------)[![Javier Marin](../Images/31800b2fbfd1f7c841c9f6a2579d5681.png)](https://javier-marin.medium.com/?source=post_page---byline--4dbdc27ccb54--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4dbdc27ccb54--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4dbdc27ccb54--------------------------------) [Javier Marin](https://javier-marin.medium.com/?source=post_page---byline--4dbdc27ccb54--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4dbdc27ccb54--------------------------------) ·阅读时间 9 分钟 ·2024年9月21日

--

随着量子硬件的进步，量子优势在特定数据生成任务中具有潜力，可能超越经典方法。量子生成对抗网络（QGANs）是合成数据生成中的一项有前景的进展，特别适用于表格数据。

# 量子电路：量子计算的通用语言

正如 Scott Aaronson 所说，一旦你把物理学剥离出来，量子计算就变得非常简单了。我们使用的量子电路就像是量子计算机的食谱或操作手册。它们逐步描述了对*量子比特*（经典比特的量子版本）进行哪些操作，以执行量子计算。这些电路能够表示和操作复杂的概率分布，而经典神经网络可能在这方面会遇到困难。这可能导致更准确地建模复杂的模式和表格数据中的相关性。通常，量子系统可以有效地表示和处理多维数据。对于具有大量特征的表格数据集，这可能会导致更紧凑和稳健的模型。这些系统具有固有的随机性，这可能在生成多样且真实的合成样本时非常有用，从而提升生成数据的整体质量和多样性。量子系统的概率性质……
