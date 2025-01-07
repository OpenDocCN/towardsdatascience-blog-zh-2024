# 理解 Shiny for Python 的两面性：Core 和 Express

> 原文：[`towardsdatascience.com/understanding-the-two-faces-of-shiny-for-python-core-and-express-7a6450b8bd59?source=collection_archive---------10-----------------------#2024-05-29`](https://towardsdatascience.com/understanding-the-two-faces-of-shiny-for-python-core-and-express-7a6450b8bd59?source=collection_archive---------10-----------------------#2024-05-29)

## 探索 Shiny Core 和 Shiny Express for Python 的区别及使用场景

[](https://medium.com/@menghani.deepsha?source=post_page---byline--7a6450b8bd59--------------------------------)![Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--7a6450b8bd59--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7a6450b8bd59--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7a6450b8bd59--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--7a6450b8bd59--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7a6450b8bd59--------------------------------) ·7 分钟阅读·2024 年 5 月 29 日

--

![](img/f0d0a35e5f331fd3ba9c92cd28c1a85f.png)

图片由[Vincent van Zalinge](https://unsplash.com/@vincentvanzalinge?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源：[Unsplash](https://unsplash.com/photos/two-giraffe-illustration-4Mu2bXIsn5Y?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

Posit 正在快速扩展其在 Python 领域的影响力。[Shiny for Python](https://shiny.posit.co/blog/posts/shiny-python-general-availability/) 于去年宣布发布，随后不久便发布了[Shiny Express](https://shiny.posit.co/blog/posts/shiny-express/)。但如果你对 Shiny 或 Python 世界比较陌生，应该首先学习哪个工具，并且如何在两者之间无缝切换呢？在本文中，我将介绍 Shiny “Core”和“Express”的结构划分以及它们的主要区别。

# Shiny 里面都有哪些内容？

> Shiny “Core”

Shiny for Python 是一个用于在 Python 中构建互动网页应用的框架。本文将介绍 Shiny Core 的一些基础组件，如果你想进一步深入学习如何使用 Shiny Core 从基础到样式构建一个网页应用，欢迎查看我之前写的[文章](https://medium.com/towards-data-science/learn-shiny-for-python-with-a-puppy-traits-dashboard-cc65f05e88c4)。

> Shiny “Express”

Shiny Express 旨在大大简化入门过程，并通过最少的样板代码编写简单的应用。

> 如果已经有 Core，为什么还需要 Express？
