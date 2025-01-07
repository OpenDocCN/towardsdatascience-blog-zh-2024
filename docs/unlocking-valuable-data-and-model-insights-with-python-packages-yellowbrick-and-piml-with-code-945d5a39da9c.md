# 解锁有价值的数据和模型洞察，使用 Python 包 Yellowbrick 和 PiML（附代码）

> 原文：[https://towardsdatascience.com/unlocking-valuable-data-and-model-insights-with-python-packages-yellowbrick-and-piml-with-code-945d5a39da9c?source=collection_archive---------3-----------------------#2024-05-17](https://towardsdatascience.com/unlocking-valuable-data-and-model-insights-with-python-packages-yellowbrick-and-piml-with-code-945d5a39da9c?source=collection_archive---------3-----------------------#2024-05-17)

## 如何获取有关模型稳健性/可靠性、训练数据量足够性等方面的洞察

[](https://theomitsa.medium.com/?source=post_page---byline--945d5a39da9c--------------------------------)[![Dr. Theophano Mitsa](../Images/a39dfae5f4409120b840cd9182b148c6.png)](https://theomitsa.medium.com/?source=post_page---byline--945d5a39da9c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--945d5a39da9c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--945d5a39da9c--------------------------------) [Dr. Theophano Mitsa](https://theomitsa.medium.com/?source=post_page---byline--945d5a39da9c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--945d5a39da9c--------------------------------) ·14分钟阅读·2024年5月17日

--

![](../Images/1dfba2f0b4168c55a6569d5b0f55f029.png)

图片由作者使用 DALL-E 创建

> “我们是我们反复做的事。因此，卓越不是一种行为，而是一种习惯。”
> 
> — 威尔·杜兰特，《哲学的故事》（1926年）

本文将探讨 *Yellowbrick* 和 *PiML* 的 Python 包如何帮助数据从业者更好地理解他们的数据和模型，解决各种数据和模型质量问题。即使在 *LLMs* 时代，它们能执行许多数据科学任务，理解和利用像 *Yellowbrick* 和 *PiML* 这样的工具仍然很重要，原因有以下几点：（a）它们为您的数据和模型提供强大的可视化洞察，（b）它们可以作为教育工具，特别是在分析模型性能方面，（c）它们在成本和资源方面高效，（d）它们提供数据隐私保护。所有的训练和可视化工作都可以在本地完成，无需将数据传输到云服务器。

我们将讨论的主题：

+   是否训练数据量足够支持分类任务

+   可视化分类性能如何受到特定参数调优的影响
