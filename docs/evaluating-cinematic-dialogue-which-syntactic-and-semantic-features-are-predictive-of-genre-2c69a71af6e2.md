# 评估电影对白——哪些句法和语义特征能预测电影类型？

> 原文：[`towardsdatascience.com/evaluating-cinematic-dialogue-which-syntactic-and-semantic-features-are-predictive-of-genre-2c69a71af6e2?source=collection_archive---------10-----------------------#2024-01-20`](https://towardsdatascience.com/evaluating-cinematic-dialogue-which-syntactic-and-semantic-features-are-predictive-of-genre-2c69a71af6e2?source=collection_archive---------10-----------------------#2024-01-20)

## 自然语言处理

## *本文探讨了电影对白与电影类型之间的关系，运用领域驱动的数据分析和有根据的特征工程。*

[](https://medium.com/@christabellecp?source=post_page---byline--2c69a71af6e2--------------------------------)![Christabelle Pabalan](https://medium.com/@christabellecp?source=post_page---byline--2c69a71af6e2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2c69a71af6e2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2c69a71af6e2--------------------------------) [Christabelle Pabalan](https://medium.com/@christabellecp?source=post_page---byline--2c69a71af6e2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2c69a71af6e2--------------------------------) ·15 分钟阅读·2024 年 1 月 20 日

--

![](img/4dd29308bfc5bc3f31c9d4051d96b164.png)

DALLE 生成的作者图像

从惊悚片中的零散对白到动作片中的脏话连篇，我们是否能仅凭对白中的语义和句法特征来推测电影类型？如果可以，哪些特征最为关键？

我们将研究剧本中的细微对话模式——其词汇、结构和节奏——是否可以成为预测电影类型的有效因素。这里的重点有两个：一是利用句法和语义的剧本特征作为预测特征，二是强调有根据的特征工程的重要性。

许多数据科学课程的一个主要短板是缺乏对领域专业知识、特征生成、工程和选择的强调。许多课程还向学生提供现成的数据集，有时这些数据集已经清理过了。而在职场中，追求快速产出往往掩盖了假设和验证过程的重要性……
