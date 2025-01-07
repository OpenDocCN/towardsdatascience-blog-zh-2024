# 机器人学中的马尔可夫决策问题

> 原文：[`towardsdatascience.com/markov-decision-problems-in-robotics-6fea564215e4?source=collection_archive---------8-----------------------#2024-11-07`](https://towardsdatascience.com/markov-decision-problems-in-robotics-6fea564215e4?source=collection_archive---------8-----------------------#2024-11-07)

## 使用一个现实世界的例子来解释 MDP、贝尔曼方程和价值迭代

[](https://medium.com/@nikolaus.correll?source=post_page---byline--6fea564215e4--------------------------------)![Nikolaus Correll](https://medium.com/@nikolaus.correll?source=post_page---byline--6fea564215e4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6fea564215e4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6fea564215e4--------------------------------) [Nikolaus Correll](https://medium.com/@nikolaus.correll?source=post_page---byline--6fea564215e4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6fea564215e4--------------------------------) ·10 分钟阅读·2024 年 11 月 7 日

--

马尔可夫决策问题（MDP）是机器人学和人工智能中的一个核心话题，因为它们是进入更复杂主题（如强化学习和部分可观察马尔可夫决策过程）的门户。在大多数在线资料中，MDP 通常使用“网格世界”示例来解释。如果你觉得这个例子很难看到现实世界中的应用，且正在寻找一个更经典的 MDP 表示，那么这篇文章就是为你准备的！在通过机器人决策问题来激发对 MDP 的兴趣后，我们将正式建模 MDP，介绍贝尔曼方程和价值迭代，并提供一个简单的 Python 实现。

*如果你不是 Medium 的订阅用户，你可以免费阅读这篇文章* [*点击这里*](https://medium.com/towards-data-science/markov-decision-problems-in-robotics-6fea564215e4?sk=c3a956e684268afe9f33821e58b1ff90)*。*

请考虑这个展示 PR2 机器人获取三明治的视频：

机器人首先开车到冰箱旁寻找三明治，但由于失败，便乘坐电梯去 Subway 买一个。虽然听起来这是一个合乎逻辑的行为顺序，但你可能会问，为什么它首先去冰箱呢？在这里，简要思考一下每个选项的优缺点是有意义的……
