# Claude 的计算机使用——直观且详尽地解释

> 原文：[`towardsdatascience.com/claudes-computer-use-intuitively-and-exhaustively-explained-5ceca60a9da8?source=collection_archive---------2-----------------------#2024-10-25`](https://towardsdatascience.com/claudes-computer-use-intuitively-and-exhaustively-explained-5ceca60a9da8?source=collection_archive---------2-----------------------#2024-10-25)

## Anthropic 是如何制作出能够控制你计算机的 AI

[](https://medium.com/@danielwarfield1?source=post_page---byline--5ceca60a9da8--------------------------------)![Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--5ceca60a9da8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ceca60a9da8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ceca60a9da8--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--5ceca60a9da8--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ceca60a9da8--------------------------------) ·阅读时长 12 分钟·2024 年 10 月 25 日

--

![](img/f764e0567efa957fab2337eff6dce353.png)

Daniel Warfield 的“数字点击器”，使用 Midjourney 制作。除非另有说明，所有图片均由作者提供。本文最初发布在[Intuitively and Exhaustively Explained](https://iaee.substack.com/)。

昨天（撰写时），Anthropic 发布了他们的新计算机使用功能演示。现在，Anthropic 的旗舰 LLM Claude 可以用于在你的计算机上执行操作。

我快速给我的订阅者发了一封邮件，介绍了这项功能是如何工作的，但我想花一些时间更深入地探讨。在这篇文章中，我们将涵盖以下内容：

+   为什么这很重要

+   演示的详细解析

+   Claude 的计算机交互（可能）如何运作，分为两部分：代理与多模态交互。

**这对谁有用？** 任何对理解新兴且令人兴奋的 AI 驱动工具感兴趣的人。

**这篇文章有多高级？** 本文包含适合初学者的、有关先进且相关的 AI 概念的描述。

**前提条件：** 无。

**免责声明：** 在撰写时，我与 Anthropic 没有任何关联。所有观点均为我个人的，且未经赞助。

# 这到底有什么大不了的？

互联网因这个演示而沸腾：
