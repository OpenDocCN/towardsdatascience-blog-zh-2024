# 优化的直观基础

> 原文：[`towardsdatascience.com/the-intuitive-basics-of-optimization-730e5b52180a?source=collection_archive---------6-----------------------#2024-06-26`](https://towardsdatascience.com/the-intuitive-basics-of-optimization-730e5b52180a?source=collection_archive---------6-----------------------#2024-06-26)

## 对优化这一神奇领域的温和介绍

[](https://medium.com/@jarom.hulet?source=post_page---byline--730e5b52180a--------------------------------)![Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--730e5b52180a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--730e5b52180a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--730e5b52180a--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--730e5b52180a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--730e5b52180a--------------------------------) ·12 分钟阅读·2024 年 6 月 26 日

--

本文将通过你的直觉和一个“日常”例子，向你介绍优化的基本组成部分。阅读完本文后，你将理解解决优化问题时（1）目标，（2）约束，（3）决策变量，（4）解空间和（5）解的定义和作用。本文旨在介绍优化的概念——并不涉及优化的数学原理或具体技术，相关内容将在未来的文章中讨论！

阅读完本文后，你将能看到优化框架如何应用于下面链接文章中的“现实世界”股票切割问题（这是我的第一篇 TDS 文章！）。

[](/how-bad-is-being-greedy-cdec75ce52a6?source=post_page-----730e5b52180a--------------------------------) ## 贪心有多糟糕？

### 对股票切割问题的贪心方法的评估

towardsdatascience.com

**内容**

1.  优化的直觉

1.  目标——我们的目标是什么？

1.  约束——我们不能做什么？

1.  决策变量和参数——我们可以控制什么，不能控制什么？

1.  可行区域与解空间——我们的最优解来自哪里？

1.  解决方案——优化推荐我们做什么？
