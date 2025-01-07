# 结合文本与符号：通向强大 LLM 推理能力的道路

> 原文：[`towardsdatascience.com/blending-text-and-symbols-a-path-to-robust-llm-reasoning-607ceebbf958?source=collection_archive---------13-----------------------#2024-01-17`](https://towardsdatascience.com/blending-text-and-symbols-a-path-to-robust-llm-reasoning-607ceebbf958?source=collection_archive---------13-----------------------#2024-01-17)

[](https://medium.com/@alcarazanthony1?source=post_page---byline--607ceebbf958--------------------------------)![Anthony Alcaraz](https://medium.com/@alcarazanthony1?source=post_page---byline--607ceebbf958--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--607ceebbf958--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--607ceebbf958--------------------------------) [Anthony Alcaraz](https://medium.com/@alcarazanthony1?source=post_page---byline--607ceebbf958--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--607ceebbf958--------------------------------) ·8 分钟阅读·2024 年 1 月 17 日

--

*人工智能软件被用来增强本文文本的语法、流畅度和可读性。*

大型语言模型（LLMs）在自然语言处理方面展示了巨大的能力。它们能够生成极其逼真的文本，进行对话，总结长篇内容，甚至尝试进行初步的推理。

然而，尽管 LLM 在语义理解方面取得了卓越的进展，但当需要复杂的逻辑推理时，它们仍面临深刻的局限性。它们的理解仍然停留在表面，常常缺乏更深层次的联系，或者在需要数学逻辑的推理中失败。

两个暴露这些大型语言模型（LLM）推理不足之处的领域是表格数据和知识图谱。表格包含结构化的统计数据、关系和属性，这些在商业分析、科学研究和公共政策等领域中随处可见。知识图谱将概念、现实世界实体及其相互关系汇集成复杂的事实网络，以图节点和边的形式呈现。

对这些结构化数据进行推理需要在上下文与符号逻辑之间微妙地平衡。例如，在表格中识别统计信息时，理解语义有助于将数字的含义放入上下文中。又如，解决分析图查询则依赖于操作逻辑图模式，同时追踪现实世界的实体。
