# LLMs的陷阱

> 原文：[https://towardsdatascience.com/llms-pitfalls-7a33de009638?source=collection_archive---------6-----------------------#2024-05-07](https://towardsdatascience.com/llms-pitfalls-7a33de009638?source=collection_archive---------6-----------------------#2024-05-07)

## 介绍一些围绕LLMs的关键组成部分，以生成生产级应用

[](https://pierpaoloippolito28.medium.com/?source=post_page---byline--7a33de009638--------------------------------)[![Pier Paolo Ippolito](../Images/981abb84149adab275473b76bdbde66f.png)](https://pierpaoloippolito28.medium.com/?source=post_page---byline--7a33de009638--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7a33de009638--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7a33de009638--------------------------------) [Pier Paolo Ippolito](https://pierpaoloippolito28.medium.com/?source=post_page---byline--7a33de009638--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7a33de009638--------------------------------) ·阅读时间9分钟·2024年5月7日

--

![](../Images/ccd96f625ecac5b4f9c80a8b6fe5b2b9.png)

AI生成（图像由作者提供）。

# 介绍

自从ChatGPT的崛起以来，大型语言模型（LLMs）变得越来越受欢迎，甚至非技术人员也开始关注它们。尽管LLMs本身尚无法提供一个完整的、可供广大受众使用的产品，但在本文中，我们将探讨一些用于使LLMs具备生产就绪能力的关键元素。

# 微调

## 数据集

像LLAMA这样的模型能够预测序列中的下一个标记，尽管这并不一定使它们适用于诸如问答等任务。因此，为了优化这些模型，可以使用不同类型的数据集：

+   **原始完成**：如果目标是预测下一个标记，我们提供一些输入文本，让模型逐步预测接下来的步骤。

+   **填充中间目标**：在这种情况下，我们有一些起始和结束文本，模型正在学习填补空白。这种方法在创建代码补全模型（如Codex）时非常流行。

+   **指令数据集**：这里的目标是教模型如何回答问题。我们有问题（指令）作为……
