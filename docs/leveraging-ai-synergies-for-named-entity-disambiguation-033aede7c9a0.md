# 利用AI协同效应进行命名实体消歧

> 原文：[https://towardsdatascience.com/leveraging-ai-synergies-for-named-entity-disambiguation-033aede7c9a0?source=collection_archive---------4-----------------------#2024-04-22](https://towardsdatascience.com/leveraging-ai-synergies-for-named-entity-disambiguation-033aede7c9a0?source=collection_archive---------4-----------------------#2024-04-22)

## [NED系列](https://medium.com/tag/ned-series)

## 结合LLMs、词汇本体和知识图谱在生物医学领域的视角

[](https://medium.com/@giuseppefutia?source=post_page---byline--033aede7c9a0--------------------------------)[![Giuseppe Futia](../Images/4d1d3b3766eca9ae8220dc5eb480a4cf.png)](https://medium.com/@giuseppefutia?source=post_page---byline--033aede7c9a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--033aede7c9a0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--033aede7c9a0--------------------------------) [Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--033aede7c9a0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--033aede7c9a0--------------------------------) ·20分钟阅读·2024年4月22日

--

*我想感谢* [*Vlasta Kus*](https://www.linkedin.com/in/vlastakus/) *对本文初步想法的反馈。*

# 介绍

命名实体消歧（NED）是自然语言处理（NLP）中的一项重要任务，旨在将含糊不清的命名实体提及解析为对应的明确实体，并映射到参考知识库中。

NED的关键思想是将一段连续文本（如“2型糖尿病”）映射到一个真实的实体，例如位于医学知识库中的[“2型糖尿病（CUI C0011860）”](https://ncim-stage.nci.nih.gov/ncimbrowser/ConceptReport.jsp?dictionary=NCI+Metathesaurus&code=C0011860)，该知识库如“统一医学语言系统”（UMLS）。NED的作用在关键领域，特别是生物医学领域尤为重要，因为高准确度地检测精确信息对于在适当的时间做出正确决策至关重要。

大型语言模型（LLMs）是能够从大量文本数据中学习模式和关系的机器学习模型，并基于这些积累和压缩的知识，生成自然语言文本。然而，由于其固有的局限性，LLMs在需要精确且详细理解人类语言的任务中效果不佳，例如命名实体消歧。此外，它们的一些...
