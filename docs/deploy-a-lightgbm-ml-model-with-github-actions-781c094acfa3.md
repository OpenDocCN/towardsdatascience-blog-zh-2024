# 使用GitHub Actions部署LightGBM机器学习模型

> 原文：[https://towardsdatascience.com/deploy-a-lightgbm-ml-model-with-github-actions-781c094acfa3?source=collection_archive---------4-----------------------#2024-06-10](https://towardsdatascience.com/deploy-a-lightgbm-ml-model-with-github-actions-781c094acfa3?source=collection_archive---------4-----------------------#2024-06-10)

## 初学者指南：如何摆脱Jupyter笔记本并部署机器学习模型

[](https://medium.com/@mattchapmanmsc?source=post_page---byline--781c094acfa3--------------------------------)[![Matt Chapman](../Images/7511deb8d9ed408ece21031f6614c532.png)](https://medium.com/@mattchapmanmsc?source=post_page---byline--781c094acfa3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--781c094acfa3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--781c094acfa3--------------------------------) [Matt Chapman](https://medium.com/@mattchapmanmsc?source=post_page---byline--781c094acfa3--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--781c094acfa3--------------------------------) ·8分钟阅读·2024年6月10日

--

![](../Images/b758cab4d12f4307ba5017f88ff8a1e8.png)

图片来自[Simone Hutsch](https://unsplash.com/@heysupersimi)于[Unsplash](https://unsplash.com/photos/three-tall-buildings-digital-wallpaper-CQphR2GPlrs)

网络上充斥着关于如何在Jupyter笔记本中训练和调优机器学习模型的教程。但是，正如[Pau Labarta Bajo](https://www.linkedin.com/posts/pau-labarta-bajo-4432074b_machinelearning-mlops-realworldml-activity-7195694289178214400-gZyw)所完美地表达的：

> 在Jupyter笔记本中的机器学习模型，其商业价值为$0。

高管们对停留在Jupyter笔记本中的概念验证模型没有兴趣。他们需要***实时***的模型，这些模型通过持续响应新数据来提供有形的价值。

在本教程中，我将向你展示如何构建一个。

我们将训练一个LightGBM机器学习模型，编写一个脚本，利用该模型为给定的输入csv文件生成得分，并将整个过程作为批量预测管道，通过GitHub Actions进行部署。

全部免费。

如果你从未将模型投入生产环境（或者你甚至不知道这句话是什么意思），那么这篇指南适合你。

# 背景：什么是“生产化”一个模型？

通常，这意味着你将模型上传到一个服务器，在那里它可以自动/反复地生成预测。
