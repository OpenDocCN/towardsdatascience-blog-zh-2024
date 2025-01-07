# 与LLM一起玩20个问题游戏

> 原文：[https://towardsdatascience.com/play-the-20-questions-game-against-an-llm-af2d324102a7?source=collection_archive---------5-----------------------#2024-12-06](https://towardsdatascience.com/play-the-20-questions-game-against-an-llm-af2d324102a7?source=collection_archive---------5-----------------------#2024-12-06)

## 学习LLM架构技术、解析输出、测试设计以及系统的性能测量

[](https://medium.com/@alejandra.vlerick?source=post_page---byline--af2d324102a7--------------------------------)[![Alejandra Vlerick](../Images/02280890ed87239c75cbcbfa7c5d686c.png)](https://medium.com/@alejandra.vlerick?source=post_page---byline--af2d324102a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af2d324102a7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--af2d324102a7--------------------------------) [Alejandra Vlerick](https://medium.com/@alejandra.vlerick?source=post_page---byline--af2d324102a7--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af2d324102a7--------------------------------) ·阅读时间：7分钟·2024年12月6日

--

![](../Images/ef35d5c09a463df416a3fce6825e0083.png)

图片由[卡尔·罗](https://unsplash.com/@carltraw?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

20个问题游戏是一个经典的猜谜游戏，适合两名玩家。一名玩家想出一个物体、人物或地方，另一名玩家轮流问是非问题，试图猜出它是什么。目标是在20个问题内猜对。如果到第20个问题时没人猜出来，思考者将揭示答案，并且回合结束。真实的游戏可以在[这里](http://20q.net/)找到，我鼓励你试着猜一些简单的东西，比如汽车或苹果。

# **本文的目标**

构建最佳的LLM代理，使其成为这款游戏中的猜测者。

# 要遵循的步骤

1.  使用[LangGraph](https://langchain.com/langgraph)构建代理

1.  使用[Pytest](https://docs.pytest.org/en/stable/)测试代理并创建你自己的代理评估

# 代码基础

从开始这个项目的第一刻起，有几件事我就非常清楚。我希望猜测者代理能够接收到一份非常清晰的、所有之前问题和答案的列表，然后被提示想出下一个要问的问题。
