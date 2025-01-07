# 我将区块链和人工智能结合起来生成艺术作品。接下来发生了什么？

> 原文：[https://towardsdatascience.com/i-combined-the-blockchain-and-ai-to-generate-art-heres-what-happened-next-859309c5d4c6?source=collection_archive---------5-----------------------#2024-12-30](https://towardsdatascience.com/i-combined-the-blockchain-and-ai-to-generate-art-heres-what-happened-next-859309c5d4c6?source=collection_archive---------5-----------------------#2024-12-30)

## 教程

## 使用大型语言模型创建数据的艺术表示

[](https://medium.com/@KoryBecker?source=post_page---byline--859309c5d4c6--------------------------------)[![Kory Becker](../Images/53a2493fe53f215d3e715d456b36c553.png)](https://medium.com/@KoryBecker?source=post_page---byline--859309c5d4c6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--859309c5d4c6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--859309c5d4c6--------------------------------) [Kory Becker](https://medium.com/@KoryBecker?source=post_page---byline--859309c5d4c6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--859309c5d4c6--------------------------------) ·7分钟阅读·6天前

--

![](../Images/1eac9faf83397d2ba2fab98d9f2d118c.png)

想象一下一个场景，代表了这些实体之间价值流动和连接的图像。包括一朵小花、一棵单独的树，使用柔和的粉彩色调。来源：Stable Diffusion。

# 观察彩虹

如果区块链能够通过图像可视化，它会是什么样子？

区块链是**分布式账本**的技术实现，最常见的用途是与金融交易相关。它远不是我们可以认为是**美丽**的东西。特别是由于区块链上存储的数据大多由复杂的数字、字母和与**价值**、发送者和接收者地址（**钱包**）以及元数据相关的符号组成。

然而，我之前曾经尝试过**生成图像**来表示[量子计算](https://medium.com/towards-data-science/qubit-magic-creating-mythical-creatures-with-quantum-computing-49bea0fabf4)，这与区块链类似，也由复杂的数字组成。我曾经想知道，是否可以结合使用人工智能、大型语言模型以及[提示工程](https://medium.com/itnext/prompt-engineering-the-magical-world-of-large-language-models-dde7d8d043ee)的相同可视化技术，从全新的数据源生成图像。

我们来试试看！

# 一切都与特征有关

要从区块链生成图像，我们首先需要识别出要利用的**特征**。
