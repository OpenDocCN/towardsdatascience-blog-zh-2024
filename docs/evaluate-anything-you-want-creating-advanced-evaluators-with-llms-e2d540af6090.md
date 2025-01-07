# 评估你想评估的任何内容 | 使用 LLMs 创建高级评估器

> 原文：[`towardsdatascience.com/evaluate-anything-you-want-creating-advanced-evaluators-with-llms-e2d540af6090?source=collection_archive---------7-----------------------#2024-04-18`](https://towardsdatascience.com/evaluate-anything-you-want-creating-advanced-evaluators-with-llms-e2d540af6090?source=collection_archive---------7-----------------------#2024-04-18)

## 探索如何为特定的现实世界需求构建自定义 LLM 评估器。

[](https://medium.com/@nikita_kiselov?source=post_page---byline--e2d540af6090--------------------------------)![Nikita Kiselov](https://medium.com/@nikita_kiselov?source=post_page---byline--e2d540af6090--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2d540af6090--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2d540af6090--------------------------------) [Nikita Kiselov](https://medium.com/@nikita_kiselov?source=post_page---byline--e2d540af6090--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2d540af6090--------------------------------) ·阅读时间 7 分钟·2024 年 4 月 18 日

--

![](img/3313d1ece51ad97e632d0c90a8f0df0b.png)

由 DALLE-3 生成的图像 | 等距风格的机器人检查

考虑到在大型语言模型（LLM）“链条”、 “代理”、聊天机器人以及其他文本生成型 AI 应用领域的快速发展，评估语言模型的表现对于理解其能力和局限性至关重要。尤其需要能够根据商业目标调整这些指标。

尽管像困惑度、BLEU 分数和句子距离等标准指标能够提供模型表现的一般性指示，但根据我的经验，**它们在捕捉现实世界应用中的细微差别和特定需求方面往往表现不佳。**

例如，考虑一个简单的 RAG 问答应用。在构建问答系统时，所谓的[“**RAG 三要素**”](https://www.trulens.org/trulens_eval/getting_started/core_concepts/rag_triad/)中的上下文相关性、事实依据和查询与回答之间的语言一致性等因素也非常重要。标准指标根本无法有效捕捉这些细微的方面。

这时，基于 LLM 的“黑箱”指标就派上用场了。尽管这个想法听起来可能天真，但基于 LLM 的“黑箱”指标背后的概念却相当有吸引力。**这些指标利用大型语言模型本身的力量来进行评估**……
