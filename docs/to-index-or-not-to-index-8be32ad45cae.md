# 索引与否

> 原文：[https://towardsdatascience.com/to-index-or-not-to-index-8be32ad45cae?source=collection_archive---------4-----------------------#2024-11-08](https://towardsdatascience.com/to-index-or-not-to-index-8be32ad45cae?source=collection_archive---------4-----------------------#2024-11-08)

## 利用SQL索引加速查询。了解何时使用索引，何时不使用索引，以及索引如何在背后运作。

[](https://medium.com/@christopher_karg?source=post_page---byline--8be32ad45cae--------------------------------)[![Christopher Karg](../Images/9d163d59e0c3167732f55d497caf9db2.png)](https://medium.com/@christopher_karg?source=post_page---byline--8be32ad45cae--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8be32ad45cae--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8be32ad45cae--------------------------------) [Christopher Karg](https://medium.com/@christopher_karg?source=post_page---byline--8be32ad45cae--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8be32ad45cae--------------------------------) ·阅读时间 18 分钟 ·2024年11月8日

--

![](../Images/7c3d3f91fd3cda8781ac71a41c224ac1.png)

来源：[https://www.pexels.com/photo/serene-autumn-morning-landscape-in-misty-meadow-29231560/](https://www.pexels.com/photo/serene-autumn-morning-landscape-in-misty-meadow-29231560/)

SQL 索引是数据圈中常常提到的一个术语 —— 你可能听过类似“只需应用一个索引”这样的说法。它也是面试中经常被问到的问题 —— “你能采取哪些步骤来提高表X的查询速度？”它是一个语法上容易实现的内容，但我发现很少有人关注实际发生了什么。在这篇文章中，我的目标正是通过使用关系型MySQL数据库（DB）来做这件事。我将涵盖什么是索引、如何实现它、它是如何工作的，以及一些在何时不使用索引的注意事项。像许多技术一样，SQL索引也有其权衡。

在我的示例中，我使用了一个来自Docker的简单MySQL容器。我并没有涵盖其如何工作的内容，但如果你有任何问题，随时可以联系我。我将展示在本文章中用来填充数据库的代码，供你根据自己的使用案例进行适配并自行实验。

我首先从一个高层次的概述开始。更详细的内容将在文章后续部分介绍。因此，我希望能够为各种技术倾向的广泛读者群体提供有价值的见解。如果你像我一样，你会发现其中的可视化效果在…
