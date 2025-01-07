# 如何修剪LLaMA 3.2及类似的大型语言模型

> 原文：[https://towardsdatascience.com/how-to-prune-llama-3-2-and-similar-large-language-models-cf18e9a2afb6?source=collection_archive---------6-----------------------#2024-11-27](https://towardsdatascience.com/how-to-prune-llama-3-2-and-similar-large-language-models-cf18e9a2afb6?source=collection_archive---------6-----------------------#2024-11-27)

## 本文介绍了一种针对最先进模型的结构化修剪技术，采用GLU架构，能够创建更小、更高效的大型语言模型。

[](https://medium.com/@peremartra?source=post_page---byline--cf18e9a2afb6--------------------------------)[![Pere Martra](../Images/18e937d347f4f726847c356d9ea5fbdc.png)](https://medium.com/@peremartra?source=post_page---byline--cf18e9a2afb6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cf18e9a2afb6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cf18e9a2afb6--------------------------------) [Pere Martra](https://medium.com/@peremartra?source=post_page---byline--cf18e9a2afb6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf18e9a2afb6--------------------------------) ·14分钟阅读·2024年11月27日

--

*免责声明：本文最初是用西班牙语写的，并通过AI工具翻译成英文，以确保准确性和一致性。您可以在此找到原始西班牙语版本* [*这里*](https://martra.uadla.com/creando-small-models-eficientes-con-llama-3-2-y-pruning/)*。*

随着大型语言模型持续增长，逐步达到更强的能力，对于更高效、更小版本的需求变得比以往任何时候都更为迫切。然而，在不失去核心功能的前提下缩小模型的尺寸是一项微妙的平衡任务。

像量化和修剪这样的技术常用于减小模型尺寸，而像知识蒸馏或迁移学习等方法则有助于保留或恢复在缩减过程中丧失的能力。

![](../Images/deb89dfb2377d7025fa02d43207ccc47.png)

图片由作者使用GPT 4生成。

在这些技术中，**修剪**作为减少模型尺寸的最有效策略之一脱颖而出。与量化通过简化数字表示不同，修剪涉及去除模型的特定部分，如神经元或整个层。然而，这种有效性是有代价的：修剪…
