# 博弈论，第2部分——好人先胜

> 原文：[https://towardsdatascience.com/game-theory-part-2-nice-guys-finished-first-8cd9022a935f?source=collection_archive---------7-----------------------#2024-10-31](https://towardsdatascience.com/game-theory-part-2-nice-guys-finished-first-8cd9022a935f?source=collection_archive---------7-----------------------#2024-10-31)

## 反复囚徒困境显示好人也能赢得最终胜利

[](https://saankhya.medium.com/?source=post_page---byline--8cd9022a935f--------------------------------)[![Saankhya Mondal](../Images/b22ffe3b52c6c3bcfafaeed3812811d8.png)](https://saankhya.medium.com/?source=post_page---byline--8cd9022a935f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8cd9022a935f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8cd9022a935f--------------------------------) [Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--8cd9022a935f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8cd9022a935f--------------------------------) ·8分钟阅读·2024年10月31日

--

与“好人总是最后失败”的普遍看法相反，博弈论揭示了好人其实可以先胜。我将在本文中通过反复囚徒困境问题来探讨这一有趣现象。这篇文章是我的博弈论系列的第二部分，如果你还没有阅读第一篇文章，建议先查看第一篇。第一部分通过克拉蒂卡和伊希塔这两位间谍的例子讨论经典的囚徒困境问题，阐明了他们的决策如何为双方带来最优结果。同时，也强调了博弈论在许多现实生活场景中的相关性。第一篇文章的链接在这里——

[](/game-theory-part-1-the-prisoners-dilemma-problem-18b216d3b523?source=post_page-----8cd9022a935f--------------------------------) [## 博弈论，第1部分——囚徒困境问题

### 博弈论在现实生活中的决策场景中有广泛的应用

towardsdatascience.com](/game-theory-part-1-the-prisoners-dilemma-problem-18b216d3b523?source=post_page-----8cd9022a935f--------------------------------)

# 反复囚徒困境问题

![](../Images/0949a91f17726e3fed3c5ebfa2f43b71.png)

图片来源：[Grab](https://unsplash.com/@grab?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在现实生活中，各方的互动并不总是一次性的事件。当然，克拉蒂卡和伊希塔这两位间谍曾面临过一次囚徒困境，最终他们都服刑7年。但在另一个平行世界里，克拉蒂卡和伊希塔面临着同样的困境（在...
