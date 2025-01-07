# 我构建了一款 AI 人类水平的游戏玩家

> 原文：[`towardsdatascience.com/i-built-an-ai-human-level-game-player-31b78df8aca3?source=collection_archive---------6-----------------------#2024-10-15`](https://towardsdatascience.com/i-built-an-ai-human-level-game-player-31b78df8aca3?source=collection_archive---------6-----------------------#2024-10-15)

## 传统的游戏树方法非常有效。

[](https://rafebrena.medium.com/?source=post_page---byline--31b78df8aca3--------------------------------)![Rafe Brena, 博士](https://rafebrena.medium.com/?source=post_page---byline--31b78df8aca3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--31b78df8aca3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31b78df8aca3--------------------------------) [Rafe Brena, 博士](https://rafebrena.medium.com/?source=post_page---byline--31b78df8aca3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31b78df8aca3--------------------------------) ·11 分钟阅读·2024 年 10 月 15 日

--

![](img/31f8cb2b72e443b5991b4d4e291f8afa.png)

照片由 [Jay Bhadreshwara](https://unsplash.com/@bhadreshwara?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/a-man-sitting-at-a-table-with-a-chess-board-7OkHRnQnpLg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

不久前，我为一款游戏构建了一个人类水平的自动玩家的决策部分。

这款棋盘游戏的自动玩家非常强大，以至于我认识的所有人（包括我自己）都无法持续战胜它，强到我不得不有意降低它的智能，以便让休闲玩家能够享受游戏。

在这篇文章中，我将解释如何使用来自 AI 技术手册中的标准方法来编程这个小型游戏的“脑力”，这些方法是在深度学习之前就已存在的。你将学习（如果你还不熟悉的话）如何构建 Minimax 树，并且如何为棋盘评估开发启发式算法。Minimax 技术可以应用于任何对抗性游戏，不论我在这里描述的游戏具体是什么。

# 于是 Kromate 诞生了

当移动设备开始流行（大约在 2008 年，iPhone 发布之后），可用的棋盘游戏非常少。而且有些游戏在当时的小屏幕上根本无法玩。比如国际象棋：你无法在一块分辨率为 320 x 480 像素、只有 3.5 英寸的屏幕上真正显示棋盘。你可以这么做，但这会非常伤眼。
