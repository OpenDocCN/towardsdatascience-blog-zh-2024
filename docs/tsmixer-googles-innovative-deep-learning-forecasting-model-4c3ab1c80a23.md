# TSMixer：Google 创新的深度学习预测模型

> 原文：[`towardsdatascience.com/tsmixer-googles-innovative-deep-learning-forecasting-model-4c3ab1c80a23?source=collection_archive---------4-----------------------#2024-12-12`](https://towardsdatascience.com/tsmixer-googles-innovative-deep-learning-forecasting-model-4c3ab1c80a23?source=collection_archive---------4-----------------------#2024-12-12)

## 结合轻量设计与高预测精度

[](https://medium.com/@nikoskafritsas?source=post_page---byline--4c3ab1c80a23--------------------------------)![Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--4c3ab1c80a23--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c3ab1c80a23--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c3ab1c80a23--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--4c3ab1c80a23--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c3ab1c80a23--------------------------------) ·阅读时长 8 分钟·2024 年 12 月 12 日

--

![](img/c267d04bd51a8d38de14ebf2e4cd37e3.png)

由 DALLE*3 创建

许多深度学习预测模型倾向于跟随 AI 趋势。

但 TSMixer 不同。它是 Google 在近一年前发布的，凭借这些优势，它在传统机器学习模型之外获得了显著的关注，并被广泛使用：

+   **轻量级：** 它基于 MLP 的架构能够高效地捕捉时间维度和特征维度中的模式。

+   **多功能：** **TSMixer-Ext**变体可以处理历史数据、未来已知输入和静态外生变量。

+   **多通道建模：** 它利用跨变量信息——其特征混合 MLP 可以联合学习协变量之间的相互依赖关系。

+   **优越的长期预测能力：** 能够处理更长的上下文，能够在预测期达到 720 个数据点，基准测试结果表明其表现优异。

这些特点使得 TSMixer 成为需求规划、零售和金融市场等领域的多功能选择。

让我们开始吧！

> *✅* 在我的[**AI 项目文件夹**](https://aihorizonforecast.substack.com/p/ai-projects)中找到适用于 TSMixer 的**实操**项目，和其他酷炫的项目！随时欢迎订阅！
