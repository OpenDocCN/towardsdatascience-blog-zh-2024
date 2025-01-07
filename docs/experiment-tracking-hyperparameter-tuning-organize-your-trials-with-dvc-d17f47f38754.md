# 实验追踪与超参数调整：使用 DVC 组织你的试验

> 原文：[https://towardsdatascience.com/experiment-tracking-hyperparameter-tuning-organize-your-trials-with-dvc-d17f47f38754?source=collection_archive---------16-----------------------#2024-03-14](https://towardsdatascience.com/experiment-tracking-hyperparameter-tuning-organize-your-trials-with-dvc-d17f47f38754?source=collection_archive---------16-----------------------#2024-03-14)

![](../Images/9e681cf7e96a2be868e783a6ff21fcc0.png)

图像由 Midjourney 生成

## 学习如何在调整模型超参数时避免迷失在众多实验中

[](https://eryk-lewinson.medium.com/?source=post_page---byline--d17f47f38754--------------------------------)[![Eryk Lewinson](../Images/56e09e19c0bbfecc582da58761d15078.png)](https://eryk-lewinson.medium.com/?source=post_page---byline--d17f47f38754--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d17f47f38754--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d17f47f38754--------------------------------) [Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--d17f47f38754--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d17f47f38754--------------------------------) ·13分钟阅读·2024年3月14日

--

在本系列的前几部分中，我已经解释了跟踪机器学习实验的好处，并展示了如何通过 DVC 简单地实现这一目标。然而，在系列中至今未深入探讨的一个方面是超参数调整（HPT）。

虽然我们的一些实验可能涉及更改数据集、代码库、添加或删除特征，或者修复一些偶发的 bug，但这些实验的数量可能仍然是可控的，因为它们需要我们手动编写代码或进行一些分析。

然而，当我们考虑超参数调整时，事情就容易失控。在之前的部分中，我展示了通过推荐的设置，我们可以轻松地通过 `params.yaml` 文件来控制模型的超参数。此外，通过使用 DVC，我们可以通过对该文件进行版本控制，轻松跟踪实验。然而，这仍然涉及根据我们的专业知识或直觉手动调整超参数。如果我们采用像网格搜索这样的程序，我们可能会用不同的超参数组合进行上千次的模型拟合和评估，而这一切仅仅在短短的时间内就完成了……
