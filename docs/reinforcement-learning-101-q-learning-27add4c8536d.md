# 强化学习 101：Q 学习

> 原文：[`towardsdatascience.com/reinforcement-learning-101-q-learning-27add4c8536d?source=collection_archive---------3-----------------------#2024-02-28`](https://towardsdatascience.com/reinforcement-learning-101-q-learning-27add4c8536d?source=collection_archive---------3-----------------------#2024-02-28)

## 解读 Q 学习、动作-价值函数和贝尔曼方程背后的数学，并在 Python 中从零开始构建它们。

[](https://medium.com/@cristianleo120?source=post_page---byline--27add4c8536d--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--27add4c8536d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--27add4c8536d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--27add4c8536d--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--27add4c8536d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--27add4c8536d--------------------------------) ·32 分钟阅读·2024 年 2 月 28 日

--

![](img/0813814464a90ae4b67445650eaf0162.png)

图像由 DALLE 生成

在上一篇文章中，我们初步了解了强化学习（RL）的世界，涵盖了基本概念，比如代理如何从其环境中学习，重点介绍了一个简单的设置——GridWorld。我们讨论了基本要素——动作、状态、奖励，以及如何在这个环境中行动。如果你是新手或需要快速回顾，建议再次阅读那篇文章，掌握基础知识后再深入了解。

[](/reinforcement-learning-101-building-a-rl-agent-0431984ba178?source=post_page-----27add4c8536d--------------------------------) ## 强化学习 101：构建一个 RL 代理

### 解读强化学习背后的数学，介绍 RL 框架，并从中构建一个 RL 模拟…

towardsdatascience.com

今天，我们准备进一步深入。我们将探索强化学习（RL）的更多复杂方面，从简单的设置过渡到动态、不断变化的环境，并寻找更复杂的方式让我们的代理在其中导航。我们将深入探讨马尔可夫决策过程（Markov Decision Process，MDP）的概念，它对于深入理解 RL 的工作原理非常重要。此外，我们还将更详细地研究 Q 学习，这是 RL 中的一个关键算法，展示了如何…
