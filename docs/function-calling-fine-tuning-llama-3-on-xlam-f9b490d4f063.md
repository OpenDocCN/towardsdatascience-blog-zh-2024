# 函数调用：在 xLAM 上微调 Llama 3

> 原文：[https://towardsdatascience.com/function-calling-fine-tuning-llama-3-on-xlam-f9b490d4f063?source=collection_archive---------4-----------------------#2024-07-23](https://towardsdatascience.com/function-calling-fine-tuning-llama-3-on-xlam-f9b490d4f063?source=collection_archive---------4-----------------------#2024-07-23)

## 由于 QLoRA 的优化，它们在速度和内存效率方面表现优异

[](https://medium.com/@bnjmn_marie?source=post_page---byline--f9b490d4f063--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--f9b490d4f063--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f9b490d4f063--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f9b490d4f063--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--f9b490d4f063--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9b490d4f063--------------------------------) ·阅读时间 8 分钟·2024年7月23日

--

![](../Images/bc60aa203270d46fd98b330d5e32fcf4.png)

由 DALL-E 生成

最近的大型语言模型（LLM）在大多数语言生成任务中表现出色。然而，由于它们是基于下一个标记预测进行操作的，它们在准确执行数学运算时经常遇到困难。此外，由于知识截止，它们可能缺乏准确回答某些问题所需的信息。

缓解这些问题的一种方法是通过函数调用。函数调用允许大型语言模型（LLM）可靠地连接到外部工具。它使得与外部 API 的交互成为可能。例如，通过将 LLM 与 Web 搜索引擎和计算器接口，便可以通过函数调用从互联网上检索信息并执行数学运算。

在本文中，我们将看到如何针对函数调用对 LLM 进行微调。我使用了 xLAM，这是 Salesforce 发布的包含 6 万条函数调用数据的微调数据集，用于微调 Llama 3。我们将看到如何格式化数据集，以及如何利用微调后的适配器来进行函数调用。

我还制作了这个笔记本，包含了本文中描述的微调代码实现，以及一些推理示例：

[获取笔记本 (#89)](https://kaitchup.substack.com/p/notebooks)

# LLM 的函数调用：如何……
