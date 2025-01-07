# 使用线性方程 + LLM 来解决 LinkedIn 皇后游戏

> 原文：[`towardsdatascience.com/using-linear-equations-llm-to-solve-linkedin-queens-game-fe9802d997e9?source=collection_archive---------5-----------------------#2024-10-07`](https://towardsdatascience.com/using-linear-equations-llm-to-solve-linkedin-queens-game-fe9802d997e9?source=collection_archive---------5-----------------------#2024-10-07)

![](img/ad35481c77af475538a173dca31d3d3f.png)

皇后（使用 Canva 生成）

## 提示 GPT 生成并解决使用 PuLP 的线性方程

[](https://memsranga.com/?source=post_page---byline--fe9802d997e9--------------------------------)![Shanmukha Ranganath](https://memsranga.com/?source=post_page---byline--fe9802d997e9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe9802d997e9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe9802d997e9--------------------------------) [Shanmukha Ranganath](https://memsranga.com/?source=post_page---byline--fe9802d997e9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe9802d997e9--------------------------------) ·阅读时间 11 分钟·2024 年 10 月 7 日

--

在我的[《我如何解决 LinkedIn 皇后游戏》](https://medium.com/towards-data-science/solving-linkedin-queens-game-cfeea7a26e86)文章中，我讨论了使用回溯法解决 LinkedIn 皇后游戏的问题，这是一种试探法。它将一个`Queen`放置在网格的某个单元格中，并继续按照约束条件放置，直到无法继续为止。这时，它会回滚上一个放置的`Queen`，并不断重复这一过程，直到找到解答或遍历完网格中的所有单元格。由于 LinkedIn 保证总是存在解，我们的回溯方法是完美的，必定能成功。然而，它的时间复杂度为`O(n²)`，但仍能在不到 0.1 秒的时间内解决，因为截至目前我玩过的最大网格只有 10x10。

[](/solving-linkedin-queens-game-cfeea7a26e86?source=post_page-----fe9802d997e9--------------------------------) ## 我如何使用回溯法解决 LinkedIn 皇后游戏

### 使用 OpenCV 自动检测谜题并重新绘制最终答案

towardsdatascience.com

尽管回溯法在时间复杂度和实现上表现良好，但我们可以更进一步，尝试寻找更好的解决方法。你知道吗，游戏中的所有约束条件都可以转化为一组线性方程？当我们解决这些方程时……
