# 持续学习：三种常见场景

> 原文：[https://towardsdatascience.com/continual-learning-the-three-common-scenarios-e6c3260fe0cb?source=collection_archive---------11-----------------------#2024-10-29](https://towardsdatascience.com/continual-learning-the-three-common-scenarios-e6c3260fe0cb?source=collection_archive---------11-----------------------#2024-10-29)

## 另外，推荐论文

[](https://pascaljanetzky.medium.com/?source=post_page---byline--e6c3260fe0cb--------------------------------)[![Pascal Janetzky](../Images/43d68509b63c5f9b3fc9cef3cbfc1a88.png)](https://pascaljanetzky.medium.com/?source=post_page---byline--e6c3260fe0cb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e6c3260fe0cb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e6c3260fe0cb--------------------------------) [Pascal Janetzky](https://pascaljanetzky.medium.com/?source=post_page---byline--e6c3260fe0cb--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e6c3260fe0cb--------------------------------) ·阅读时间：6分钟·2024年10月29日

--

随着机器学习模型的训练成本上升[1]，持续学习（CL）作为一种有效的应对措施应运而生。在CL中，机器学习模型（*例如*，像GPT这样的LLM）在不断到来的数据流（*例如*，文本数据）上进行训练。关键在于，在CL中数据不能被存储，因此只有最新的数据可用于训练。主要挑战是如何在当前数据（通常称为*任务*）上进行训练，同时又不忘记从旧任务中学到的知识。避免遗忘旧知识至关重要，因为在测试时，模型会在所有已见任务的测试数据上进行评估。这个挑战通常在文献中被称为灾难性遗忘，且它是稳定性-可塑性权衡的一部分。

一方面，稳定性-可塑性权衡指的是保持网络参数（*例如*，层权重）稳定，以避免遗忘（稳定性）。另一方面，它意味着允许参数发生变化，以便从新任务中学习（可塑性）。CL方法从多个角度处理这一权衡，我在[上一篇文章](https://medium.com/towards-data-science/continual-learning-a-primer-e328ed1d072f)中曾提到过。

![](../Images/67d4ce51f17fed060c52239abcd559c4.png)

图片来源：[lionel mermoz](https://unsplash.com/@mermoz?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

今天文章的重点是CL研究中反复出现的基本场景：类别增量学习、领域增量学习和任务增量学习。
