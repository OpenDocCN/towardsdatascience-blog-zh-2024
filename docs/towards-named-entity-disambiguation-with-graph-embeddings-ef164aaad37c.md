# 朝着命名实体消歧（NED）与图嵌入的方向发展

> 原文：[`towardsdatascience.com/towards-named-entity-disambiguation-with-graph-embeddings-ef164aaad37c?source=collection_archive---------2-----------------------#2024-09-25`](https://towardsdatascience.com/towards-named-entity-disambiguation-with-graph-embeddings-ef164aaad37c?source=collection_archive---------2-----------------------#2024-09-25)

## [NED-SERIES](https://medium.com/tag/ned-series)

## 如何结合预训练语言模型与图机器学习从生物医学文本中提取知识

[](https://medium.com/@giuseppefutia?source=post_page---byline--ef164aaad37c--------------------------------)![Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--ef164aaad37c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ef164aaad37c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef164aaad37c--------------------------------) [Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--ef164aaad37c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef164aaad37c--------------------------------) ·10 分钟阅读·2024 年 9 月 25 日

--

*本文总结了在 IEEE 信息与通信技术应用（AICT 2024）会议上接受的一篇论文，该论文荣获了* ***最佳论文奖***。 

*除了签名人外，论文的合著者包括 Felice Paolo Colliani（第一作者）、Giovanni Garifo、Antonio Vetrò 和 Juan Carlos De Martin。*

*完整文章可以在此处访问：* [*https://ieeexplore.ieee.org/abstract/document/10740424*](https://ieeexplore.ieee.org/abstract/document/10740424)*.*

# 引言

由于科学研究的增长、技术的进步以及全球对医疗保健和医学研究的重视，生物医学领域的出版率多年来稳步增长。

自然语言处理（NLP）技术在生物医学领域的应用代表着在分析和解读庞大的生物医学知识库方面的一次转变，增强了我们从文本数据中提取有意义洞察的能力。

命名实体消歧（NED）是一个关键的 NLP 任务，涉及通过将实体提及链接到知识库中的正确条目来解决歧义。为了理解此类任务的重要性和复杂性...
