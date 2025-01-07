# 使用 DVC 的实验追踪功能来跟踪你的回测

> 原文：[https://towardsdatascience.com/keep-track-of-your-backtests-with-dvcs-experiment-tracking-38977cbba4a9?source=collection_archive---------9-----------------------#2024-01-05](https://towardsdatascience.com/keep-track-of-your-backtests-with-dvcs-experiment-tracking-38977cbba4a9?source=collection_archive---------9-----------------------#2024-01-05)

![](../Images/12ce9be8dc77ba65759a2d4148e24f3a.png)

图片由 Midjourney 生成

## 本教程第 4 部分，讲解如何使用 DVC 进行实验追踪，这次是时间序列预测

[](https://eryk-lewinson.medium.com/?source=post_page---byline--38977cbba4a9--------------------------------)[![Eryk Lewinson](../Images/56e09e19c0bbfecc582da58761d15078.png)](https://eryk-lewinson.medium.com/?source=post_page---byline--38977cbba4a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38977cbba4a9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--38977cbba4a9--------------------------------) [Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--38977cbba4a9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38977cbba4a9--------------------------------) ·阅读时长 11 分钟·2024年1月5日

--

在本系列的前几部分中，我演示了如何使用 DVC 进行实验追踪。只需稍微多一点努力，你就可以停止在纸上或电子表格中记录实验结果了。

你可以在下面找到本系列的前几部分：

+   第 1 部分: [将 VS Code 打造成机器学习实验的一站式商店](/turn-vs-code-into-a-one-stop-shop-for-ml-experiments-49c97c47db27)

+   第 2 部分: [通过实时图表提升你的机器学习实验工作流](/enhance-your-ml-experimentation-workflow-with-real-time-plots-434106b1a1c2)

+   第 3 部分: [DVC 实验追踪的极简主义指南](/the-minimalists-guide-to-experiment-tracking-with-dvc-f07e4636bdbb)

在前几部分中，我们看到了一些分类问题的实验追踪示例。在时间序列预测中，我们使用不同的方法来验证模型。因此，我们可能还需要追踪更多关于回测的元数据，并保存多个聚合组的得分。

在我上一篇关于回测的文章中 [回顾你的预测模型：回测指南](/putting-your-forecasting-model-to-the-test-a-guide-to-backtesting-24567d377fb5)，我创建了一个自定义类，使我能够轻松评估我的预测模型。然后，我在 Jupyter Notebook 中运行了多个回测，并将结果存储在一个大的字典中。尽管这种方法…
