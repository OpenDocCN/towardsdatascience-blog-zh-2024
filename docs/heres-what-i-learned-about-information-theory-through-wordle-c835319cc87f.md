# 这是我通过Wordle学到的关于信息理论的知识

> 原文：[https://towardsdatascience.com/heres-what-i-learned-about-information-theory-through-wordle-c835319cc87f?source=collection_archive---------5-----------------------#2024-12-09](https://towardsdatascience.com/heres-what-i-learned-about-information-theory-through-wordle-c835319cc87f?source=collection_archive---------5-----------------------#2024-12-09)

## 改善猜测的科学

[](https://saankhya.medium.com/?source=post_page---byline--c835319cc87f--------------------------------)[![Saankhya Mondal](../Images/b22ffe3b52c6c3bcfafaeed3812811d8.png)](https://saankhya.medium.com/?source=post_page---byline--c835319cc87f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c835319cc87f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c835319cc87f--------------------------------) [Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--c835319cc87f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c835319cc87f--------------------------------) ·阅读时长8分钟·2024年12月9日

--

Wordle是由纽约时报开发的一款上瘾的在线每日单词谜题游戏。

规则很简单。玩家有六次机会来猜一个五个字母的单词。Wordle会根据你每次猜测的结果，使用绿色、灰色和黄色的颜色来反馈你的猜测单词中的字母。

绿色表示你猜对了给定字母和位置。黄色表示该字母在单词中存在，但位置不对。灰色表示该字母在单词中不存在。

完全正确的猜测会使所有五个字母变成绿色。错误的猜测可能会导致所有字母变成灰色。如果猜测部分正确，你可能会看到黄色和灰色字母混合的情况，表示某些字母存在但位置不对。

我相信许多读者都玩过Wordle，但如果你还没玩过，可以在[这里](https://www.nytimes.com/games/wordle/index.html)试试。

![](../Images/7fea97194cd62d1b6a06b513365dee2d.png)

来自Wordle [网站](https://www.nytimes.com/games/wordle/index.html)的截图

由于其有趣的反馈循环，帮助缩小可能的单词列表，Wordle是信息理论如何提升决策制定的完美示例。我玩这个游戏是为了娱乐，但我内心的书呆子却想更深入地探索我猜测背后的“原因”。是什么让某些猜测…
