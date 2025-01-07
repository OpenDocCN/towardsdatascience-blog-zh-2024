# GPT 与额头侦探

> 原文：[https://towardsdatascience.com/gpts-and-the-forehead-detective-ea05440c2c63?source=collection_archive---------7-----------------------#2024-09-05](https://towardsdatascience.com/gpts-and-the-forehead-detective-ea05440c2c63?source=collection_archive---------7-----------------------#2024-09-05)

## OpenAI LLMs 的推理能力足够好，可以玩经典的猜谜游戏吗？

[](https://medium.com/@krzysztof.kornel?source=post_page---byline--ea05440c2c63--------------------------------)[![Krzysztof K. Zdeb](../Images/4531b37707bf6a01ef635e4b9ecfc03f.png)](https://medium.com/@krzysztof.kornel?source=post_page---byline--ea05440c2c63--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ea05440c2c63--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ea05440c2c63--------------------------------) [Krzysztof K. Zdeb](https://medium.com/@krzysztof.kornel?source=post_page---byline--ea05440c2c63--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ea05440c2c63--------------------------------) ·11分钟阅读·2024年9月5日

--

![](../Images/336d8233221306b9c75d08de4698da39.png)

GPT 玩“额头侦探”游戏 | 图片来自 DALL·E

每当我和家人一起进行长途旅行时，我们喜欢用这个时间玩各种口头游戏，特别是那些不需要我作为司机过多集中注意力的游戏。我们最喜欢的游戏之一是角色猜谜游戏。规则很简单：一个人想到一个角色——可以是真实的也可以是虚构的——其余的人尝试猜出这个角色是谁。我们只能问只能用简单的“是”或“不是”来回答的封闭式问题。想到角色的人只能用这些答案来回应。我们轮流提问，首先正确猜出角色的人获胜。所以这其实就像“额头侦探”游戏，只不过我们没有把便签贴在头上。

在我们最近的一次旅行中，我们正在玩这个游戏，但过了一段时间后，我们开始没什么新想法来猜测角色。这时我想：“为什么不让 ChatGPT 来接管这个任务呢？”我在手机上安装了 ChatGPT 应用，于是我打开它，让 ChatGPT 想出一个角色供我们猜测。我还指示它回答我们关于角色的猜测。于是，我们开始和 ChatGPT 一起玩。它想出了一个角色，我们轮流提问。人工智能……
