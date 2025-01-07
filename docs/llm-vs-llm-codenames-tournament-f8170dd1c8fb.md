# LLM 对 LLM：Codenames 比赛

> 原文：[`towardsdatascience.com/llm-vs-llm-codenames-tournament-f8170dd1c8fb?source=collection_archive---------6-----------------------#2024-10-12`](https://towardsdatascience.com/llm-vs-llm-codenames-tournament-f8170dd1c8fb?source=collection_archive---------6-----------------------#2024-10-12)

## 三个不同的 LLM 代理之间的迷你多代理竞赛

[](https://medium.com/@artfish?source=post_page---byline--f8170dd1c8fb--------------------------------)![Yennie Jun](https://medium.com/@artfish?source=post_page---byline--f8170dd1c8fb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f8170dd1c8fb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f8170dd1c8fb--------------------------------) [Yennie Jun](https://medium.com/@artfish?source=post_page---byline--f8170dd1c8fb--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f8170dd1c8fb--------------------------------) ·阅读时长：6 分钟·2024 年 10 月 12 日

--

![](img/eea2317dcfb4c6baaffc4fcb5ed49da3.png)

由 ChatGPT 4o 生成。

*本文最初发布于* [*Art Fish Intelligence*](https://www.artfish.ai/p/llm-codenames-competition)*.*

# 介绍

大型语言模型（LLMs）擅长许多事情，其中之一就是玩游戏。人们已经利用 LLMs 玩过各种游戏，例如 [Minecraft](https://arxiv.org/abs/2305.16291)、[国际象棋](https://proceedings.neurips.cc/paper_files/paper/2023/file/16b14e3f288f076e0ca73bdad6405f77-Paper-Datasets_and_Benchmarks.pdf)、[谋杀之谜游戏](https://arxiv.org/abs/2404.17662)、[狼人杀](https://arxiv.org/abs/2309.04658) 和 [纽约时报连接谜题](https://arxiv.org/abs/2407.11240)。（欲了解更全面的列表，您可以参考 [这篇调查](https://github.com/git-disl/awesome-LLM-game-agent-papers)。）

上面的大部分示例展示了 LLMs 在与自己或人类对战时玩游戏。但，LLMs 与*其他*LLMs 对战时，表现如何呢？

在这篇文章中，我展示了三种不同的 LLM 在流行桌游 Codenames 中的竞争结果，该游戏挑战玩家在看似无关的单词中寻找模式。

# Codenames

对于不熟悉的人，[Codenames](https://en.m.wikipedia.org/wiki/Codenames_(board_game)) 是一款由 [Vladimír Chvátil](https://en.m.wikipedia.org/wiki/Vladim%C3%ADr_Chv%C3%A1til) 创建的桌游。游戏将两队（通常是红队和蓝队）对抗。

每个队伍有一个间谍头目，负责给出单词线索，指向 5x5 的单词板上的多个单词。队伍中的其他玩家必须猜测自己队伍的单词，同时避免猜到属于对方队伍的单词。只有…
