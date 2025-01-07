# 为什么表示微调是目前最有效的方式？

> 原文：[https://towardsdatascience.com/why-representation-finetuning-is-the-most-efficient-approach-today-d589c2535c77?source=collection_archive---------0-----------------------#2024-05-26](https://towardsdatascience.com/why-representation-finetuning-is-the-most-efficient-approach-today-d589c2535c77?source=collection_archive---------0-----------------------#2024-05-26)

## **表示微调 LLAMA3的逐步指南**

[](https://medium.com/@yanli.liu?source=post_page---byline--d589c2535c77--------------------------------)[![Yanli Liu](../Images/31342655ab635eb38e3ce501235f1b89.png)](https://medium.com/@yanli.liu?source=post_page---byline--d589c2535c77--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d589c2535c77--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d589c2535c77--------------------------------) [Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--d589c2535c77--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d589c2535c77--------------------------------) ·阅读时间：11分钟·2024年5月26日

--

你知道吗，实际上只用少量的参数和一个只有10个数据点的小数据集，就可以微调一个语言模型？

嗯，这不是魔法。

![](../Images/9c2968b9ec79151e9f6f30b26a6047ae.png)

照片由[Mrika Selimi](https://unsplash.com/@mrika?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我遇到了一种新方法，称为[“**表示微调**”](https://arxiv.org/pdf/2404.03592)，这是由著名的斯坦福NLP团队开发的。

最近的参数高效微调（PEFT）方法，如低秩适应（LoRA），使得你只需更新少量参数即可微调大型语言模型。这节省了许多时间和计算资源，是一大进步。

但还有一种更简洁的方法：**表示微调**。它涉及更少的参数，并且表现得更好。它不是更新权重，而是操作表示层的激活值。

我们首先向你介绍**表示微调**，然后将其与PEFT方法进行比较。接着我们展示如何在实践中应用这种方法，使用LLAMA3。这个方法适用于任何来自HuggingFace的预训练语言模型。

让我们通过一个例子，展示如何配置LLAMA3来过滤掉医学建议——一个仅包含五个数据点的简单数据集……
