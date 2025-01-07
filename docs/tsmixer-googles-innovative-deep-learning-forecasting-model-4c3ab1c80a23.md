# TSMixer：Google创新的深度学习预测模型

> 原文：[https://towardsdatascience.com/tsmixer-googles-innovative-deep-learning-forecasting-model-4c3ab1c80a23?source=collection_archive---------4-----------------------#2024-12-12](https://towardsdatascience.com/tsmixer-googles-innovative-deep-learning-forecasting-model-4c3ab1c80a23?source=collection_archive---------4-----------------------#2024-12-12)

## 结合轻量设计与高预测精度

[](https://medium.com/@nikoskafritsas?source=post_page---byline--4c3ab1c80a23--------------------------------)[![Nikos Kafritsas](../Images/de965cfcd8fbd8e1baf849017d365cbb.png)](https://medium.com/@nikoskafritsas?source=post_page---byline--4c3ab1c80a23--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c3ab1c80a23--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4c3ab1c80a23--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--4c3ab1c80a23--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c3ab1c80a23--------------------------------) ·阅读时长8分钟·2024年12月12日

--

![](../Images/c267d04bd51a8d38de14ebf2e4cd37e3.png)

由DALLE*3创建

许多深度学习预测模型倾向于跟随AI趋势。

但TSMixer不同。它是Google在近一年前发布的，凭借这些优势，它在传统机器学习模型之外获得了显著的关注，并被广泛使用：

+   **轻量级：** 它基于MLP的架构能够高效地捕捉时间维度和特征维度中的模式。

+   **多功能：** **TSMixer-Ext**变体可以处理历史数据、未来已知输入和静态外生变量。

+   **多通道建模：** 它利用跨变量信息——其特征混合MLP可以联合学习协变量之间的相互依赖关系。

+   **优越的长期预测能力：** 能够处理更长的上下文，能够在预测期达到720个数据点，基准测试结果表明其表现优异。

这些特点使得TSMixer成为需求规划、零售和金融市场等领域的多功能选择。

让我们开始吧！

> *✅* 在我的[**AI项目文件夹**](https://aihorizonforecast.substack.com/p/ai-projects)中找到适用于TSMixer的**实操**项目，和其他酷炫的项目！随时欢迎订阅！
