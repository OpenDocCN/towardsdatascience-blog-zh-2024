# 如何改善图表，以提升机器学习模型的性能

> 原文：[https://towardsdatascience.com/how-to-improve-graphs-to-empower-your-machine-learning-models-performance-f7a533a73fc2?source=collection_archive---------7-----------------------#2024-04-05](https://towardsdatascience.com/how-to-improve-graphs-to-empower-your-machine-learning-models-performance-f7a533a73fc2?source=collection_archive---------7-----------------------#2024-04-05)

## 了解如何改善你的图表，以便用于机器学习任务。

[](https://oieivind.medium.com/?source=post_page---byline--f7a533a73fc2--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--f7a533a73fc2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f7a533a73fc2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f7a533a73fc2--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--f7a533a73fc2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f7a533a73fc2--------------------------------)·14分钟阅读·2024年4月5日

--

由拓扑信息定义的图表在许多机器学习场景中非常有用。它们可用于社区检测、节点影响、分类等任务。机器学习模型在这些任务上能达到的性能将极大地依赖于图表的质量，这使得改善图表质量变得尤为重要。由于图表质量的重要性，本文将讨论如何改善用于机器学习的图表质量。

![](../Images/7649ad1623dd1dc718f523c6bea34f17.png)

了解如何在本文中改善图表。图像由ChatGPT生成。“为一篇标题为：如何改善由拓扑信息定义的图表”的文章生成图像提示。*ChatGPT*，4，OpenAI，2024年4月3日。[https://chat.openai.com.](https://chat.openai.com.)

# 动机

本文的动机来源于我正在进行的一个涉及图表的项目。我创建的图表质量对我的社区聚类算法的性能至关重要，这也是我花费大量时间理论化如何提升图表质量的原因。我将会在本文中提到的每个想法，我都在我自己的图表上进行了测试。一些想法改善了我的图表质量，另一些则降低了质量，还有一些对质量没有影响。如果你想了解每个想法对图表的影响，可以阅读我在《Towards Data Science》上关于测试图表质量的文章：
