# 基于图的提示与推理在语言模型中的应用

> 原文：[https://towardsdatascience.com/graph-based-prompting-and-reasoning-with-language-models-d6acbcd6b3d8?source=collection_archive---------0-----------------------#2024-01-03](https://towardsdatascience.com/graph-based-prompting-and-reasoning-with-language-models-d6acbcd6b3d8?source=collection_archive---------0-----------------------#2024-01-03)

## 理解图形思维提示及其几种变体……

[](https://wolfecameron.medium.com/?source=post_page---byline--d6acbcd6b3d8--------------------------------)[![Cameron R. Wolfe, Ph.D.](../Images/52bb88d7cf1105501be2fae5ccbe7a03.png)](https://wolfecameron.medium.com/?source=post_page---byline--d6acbcd6b3d8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6acbcd6b3d8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d6acbcd6b3d8--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--d6acbcd6b3d8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6acbcd6b3d8--------------------------------) ·阅读时长22分钟·2024年1月3日

--

![](../Images/1b6c41b4f07a4b381d9da086ab84a80c.png)

(图片由[Alina Grubnyak](https://unsplash.com/@alinnnaaaa?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/low-angle-photography-of-metal-structure-ZiQkhI7417A?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash))

高级提示技术，如思维链[8]和思维树[9]，显著提高了大型语言模型（LLM）在解决复杂推理任务中的能力。大体而言，迫使LLM以逐步响应的方式来处理问题，极大地提升了其问题解决能力。然而，这些技术假设推理过程应该遵循一种从一个思维到下一个思维的线性模式。值得注意的是，人类的推理过程往往与此不同，通常遵循多条不同的思维链，甚至将来自不同思维的见解结合起来，从而得出最终解决方案。在本概述中，我们将研究几种将推理过程建模为图结构的提示技术——而不是链式或树形结构——这种方式更好地捕捉到推理过程中的各种非线性模式。

> “人类思维的特点之一是其能够在看似无关的想法之间进行突然的跳跃和连接，这往往能引发新的见解和解决方案。这种非线性的、跳跃性的思维过程是人类创造力、推理和问题解决能力的标志……
