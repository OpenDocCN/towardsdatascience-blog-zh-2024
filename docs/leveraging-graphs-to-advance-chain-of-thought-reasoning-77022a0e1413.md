# 利用图表推进思维链推理

> 原文：[https://towardsdatascience.com/leveraging-graphs-to-advance-chain-of-thought-reasoning-77022a0e1413?source=collection_archive---------11-----------------------#2024-01-16](https://towardsdatascience.com/leveraging-graphs-to-advance-chain-of-thought-reasoning-77022a0e1413?source=collection_archive---------11-----------------------#2024-01-16)

[](https://medium.com/@alcarazanthony1?source=post_page---byline--77022a0e1413--------------------------------)[![Anthony Alcaraz](../Images/6a71a1752677bd07c384246fb0c7f7e8.png)](https://medium.com/@alcarazanthony1?source=post_page---byline--77022a0e1413--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--77022a0e1413--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--77022a0e1413--------------------------------) [Anthony Alcaraz](https://medium.com/@alcarazanthony1?source=post_page---byline--77022a0e1413--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--77022a0e1413--------------------------------)·阅读时间 5 分钟·2024 年 1 月 16 日

--

*本文的文本经过人工智能软件的处理，以增强语法、流畅性和可读性。*

思维链（CoT）提示迅速成为一种技术，大幅提升大型语言模型的推理能力。通过逐步展示推理链，CoT 允许像 GPT-4 这样的模型解决多步问题 — 从数学到常识谜题。关键见解是通过从上下文示例中学习，模型可以获得复杂的逻辑技能，而无需诉诸于任务特定的微调。

然而，限制 CoT 提示更广泛适用性的关键是依赖手工设计的演示。制作具有连贯逻辑流的高质量推理链需要大量的人力和专业知识。为了释放全部潜力，我们需要自动生成高质量 CoT 演示的方法。

最近的工作通过检索和生成方法来解决这个问题。但是由此产生的链条往往存在不连贯、间隙和基础错误。在文本序列中捕捉推理链的流动、概念性流程已被证明是困难的。我们提出用专门的图表代替以推进 CoT 提示的推理结构。

具体来说，本文确定了两种互补的图表驱动技术：

1.  将 CoT 演示建模为有向图，以捕捉流动并分析结构
