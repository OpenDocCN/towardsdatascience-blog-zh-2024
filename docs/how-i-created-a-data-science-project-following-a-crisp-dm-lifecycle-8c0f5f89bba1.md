# 我如何按照CRISP-DM生命周期创建数据科学项目

> 原文：[https://towardsdatascience.com/how-i-created-a-data-science-project-following-a-crisp-dm-lifecycle-8c0f5f89bba1?source=collection_archive---------3-----------------------#2024-11-13](https://towardsdatascience.com/how-i-created-a-data-science-project-following-a-crisp-dm-lifecycle-8c0f5f89bba1?source=collection_archive---------3-----------------------#2024-11-13)

## 一个使用CRISP-DM框架的端到端项目

[](https://gustavorsantos.medium.com/?source=post_page---byline--8c0f5f89bba1--------------------------------)[![Gustavo R Santos](../Images/a19a9f4525cdeb6e7a76cd05246aa622.png)](https://gustavorsantos.medium.com/?source=post_page---byline--8c0f5f89bba1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8c0f5f89bba1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8c0f5f89bba1--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--8c0f5f89bba1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8c0f5f89bba1--------------------------------) ·22分钟阅读·2024年11月13日

--

![](../Images/0dfa5375c7810775d0d6f3be3cea0f0c.png)

由AI生成的图像。OpenAI。*CRISP-DM数据科学项目生命周期框架*。DALL-E，2024年。

# 介绍

CRISP-DM代表**跨行业数据挖掘标准过程**，这是一个开放的数据挖掘框架，任何人都可以使用它。

它的第一个版本由SPSS、戴姆勒-奔驰和NCR创建。后来，一组公司开发并演变成了CRISP-DM，如今它已经成为数据科学领域最著名和最广泛采用的框架之一。

该过程由6个阶段组成，且具有灵活性。它更像是一个*活的有机体*，你可以（而且可能应该）在各个阶段之间来回迭代与改进，提升结果。

该过程包括以下几个阶段：

> 业务理解
> 
> 数据理解
> 
> 数据准备
> 
> 建模
> 
> 评估
> 
> 部署

小箭头展示了从业务理解到部署的自然路径——直接发生交互的地方——而圆圈则表示阶段之间的循环关系。这意味着项目并不会在部署后结束，而是可以因为项目触发的新业务问题而重新启动，或者…
