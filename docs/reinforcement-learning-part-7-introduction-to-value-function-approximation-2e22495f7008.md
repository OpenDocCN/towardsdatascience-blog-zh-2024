# 强化学习，第7部分：值函数逼近简介

> 原文：[https://towardsdatascience.com/reinforcement-learning-part-7-introduction-to-value-function-approximation-2e22495f7008?source=collection_archive---------5-----------------------#2024-08-22](https://towardsdatascience.com/reinforcement-learning-part-7-introduction-to-value-function-approximation-2e22495f7008?source=collection_archive---------5-----------------------#2024-08-22)

## 将强化学习从表格方法扩展到大空间

[](https://medium.com/@slavahead?source=post_page---byline--2e22495f7008--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--2e22495f7008--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e22495f7008--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2e22495f7008--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--2e22495f7008--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e22495f7008--------------------------------) ·阅读时间：10分钟·2024年8月22日

--

![](../Images/139ab7f249ac9416dbe2cbd6d9ddd302.png)

**强化学习**是机器学习中的一个领域，它引入了智能体在复杂环境中学习最佳策略的概念。智能体通过自己的行动来学习，而这些行动会根据环境的状态产生奖励。强化学习是一个具有挑战性的话题，与机器学习的其他领域有显著的不同。

强化学习的一个显著特点是，同样的算法可以用于使智能体适应完全不同、未知且复杂的条件。

> **注意**：为了充分理解本文中的概念，强烈推荐先熟悉[之前的文章](https://medium.com/@slavahead/list/reinforcement-learning-78e5b7cb3292)中讨论的概念。

![Vyacheslav Efimov](../Images/cc2f0c63d16a57528c9b52db8ca79174.png)

[Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page-----2e22495f7008--------------------------------)

## 强化学习

[查看列表](https://medium.com/@slavahead/list/reinforcement-learning-78e5b7cb3292?source=post_page-----2e22495f7008--------------------------------)8篇故事![](../Images/2b185f2bfd089377f114107756707143.png)![](../Images/af159ecc7d031cb93b885561379c4d21.png)![](../Images/57b903ec1ae525ae1f0094f47b4c90a3.png)

# 关于本文

到目前为止，我们只讨论了**表格**强化学习方法。在这种情况下，“表格”一词表示所有可能的…
