# SQL解释：分组集、汇总和立方体

> 原文：[https://towardsdatascience.com/sql-explained-grouping-sets-rollup-and-cube-a097456fb9a2?source=collection_archive---------8-----------------------#2024-05-09](https://towardsdatascience.com/sql-explained-grouping-sets-rollup-and-cube-a097456fb9a2?source=collection_archive---------8-----------------------#2024-05-09)

![](../Images/b1218bb6e07d95b3355e500cc4f3c4ff.png)

图片来自AI（Dalle-3）

## 它们是什么，如何使用它们？

[](https://medium.com/@thomas_reid?source=post_page---byline--a097456fb9a2--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--a097456fb9a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a097456fb9a2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a097456fb9a2--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--a097456fb9a2--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a097456fb9a2--------------------------------) ·7分钟阅读·2024年5月9日

--

我是SQL的长期用户，但有一件事我一直没能完全掌握，那就是现代SQL数据库系统在多年前引入的分组集（grouping sets）、立方体（cube）和汇总（rollup）功能。

其中一个原因可能是我所使用和查询的数据库类型。它们大多是OLTP数据库系统，而在我的日常工作中，我似乎从未需要使用这些运算符。

我的另一部分其实并不完全理解它们是如何工作的。我发现相关文档令人困惑，因此我最终将它们搁置在了比喻意义上的**“知道就好，但不需要它们”**堆里。

那么，发生了什么变化呢？实际上没有变化，但就个人而言，我就是讨厌当我所使用的系统引入我完全不理解或不用的功能时。

我常常回想起窗口函数首次成为SQL的一部分时的类似情况。我立即接受了***它们***，大量使用它们，变得相当熟练，并能够用它们做一些在常规SQL中非常困难甚至不可能做到的事情。

所以，可能是因为我工作中使用的数据库类型，我花了十多年才重新审视这些分组功能……
