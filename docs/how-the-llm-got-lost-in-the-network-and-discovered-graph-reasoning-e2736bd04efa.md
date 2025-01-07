# 大语言模型如何在网络中迷失并发现图形推理

> 原文：[https://towardsdatascience.com/how-the-llm-got-lost-in-the-network-and-discovered-graph-reasoning-e2736bd04efa?source=collection_archive---------5-----------------------#2024-09-12](https://towardsdatascience.com/how-the-llm-got-lost-in-the-network-and-discovered-graph-reasoning-e2736bd04efa?source=collection_archive---------5-----------------------#2024-09-12)

## |图形|大语言模型|推理|图形推理|

## 提升大语言模型：通过图形推理与指令调优的旅程

[](https://salvatore-raieli.medium.com/?source=post_page---byline--e2736bd04efa--------------------------------)[![Salvatore Raieli](../Images/6bb4520e2df40d20283e7283141b5e06.png)](https://salvatore-raieli.medium.com/?source=post_page---byline--e2736bd04efa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2736bd04efa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2736bd04efa--------------------------------) [Salvatore Raieli](https://salvatore-raieli.medium.com/?source=post_page---byline--e2736bd04efa--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2736bd04efa--------------------------------) ·阅读时间10分钟·2024年9月12日

--

![](../Images/fd85685e726eb0e5b4ab19198729788d.png)

该图像由作者使用人工智能创建

> 在一个长篇故事的格式中，你必须为你的角色设定一个图形。—— 苏尼尔·格罗弗

[大语言模型 (LLMs)](https://github.com/SalvatoreRa/tutorial/blob/main/artificial%20intelligence/FAQ.md#:~:text=What%20is%20a%20Large%20Language%20Model%20(LLM)%3F) 展现了令人难以置信的能力，这些能力最近已经扩展到文本之外。一方面，我们见证了多模态模型（例如，[视觉-语言模型](https://huggingface.co/blog/vlms)）；另一方面，我们也见证了模型能力的扩展，涵盖了需要[推理](https://github.com/atfortes/Awesome-LLM-Reasoning)的技能。例如，现在我们有专门用于解决数学问题或编写代码的模型。

然而，最近，另一种数据类型引起了研究人员的关注。事实上，现实世界中的大量数据可以以图形的形式表示。例如，社交网络就是一种以图形结构组织的数据，因为表示各种实体之间的关系非常重要。这并不是唯一的例子：在生物医学科学中，通常使用图形表示[分子](https://en.wikipedia.org/wiki/Molecular_graph#:~:text=the%20following%20classical%20definition%20is,graph%20correspond%20to%20chemical%20bonds)，以及[蛋白质之间的相互作用](https://arxiv.org/abs/2404.10450)。然而，大语言模型与图形的交互是最近才出现的……
