# 告别令人困惑的 Python 错误信息

> 原文：[`towardsdatascience.com/one-line-of-code-to-say-goodbye-to-confusing-python-error-messages-8090ee6dd046?source=collection_archive---------1-----------------------#2024-03-03`](https://towardsdatascience.com/one-line-of-code-to-say-goodbye-to-confusing-python-error-messages-8090ee6dd046?source=collection_archive---------1-----------------------#2024-03-03)

![](img/276b9b731470da1b3c6f34f847d78b68.png)

图片来源：[15548337](https://pixabay.com/users/15548337-15548337/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4918187) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4918187)

## 灵活的错误信息增强库 — PrettyError

[](https://christophertao.medium.com/?source=post_page---byline--8090ee6dd046--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--8090ee6dd046--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8090ee6dd046--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8090ee6dd046--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--8090ee6dd046--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8090ee6dd046--------------------------------) ·阅读时长 8 分钟 ·2024 年 3 月 3 日

--

编程是一项活动，我们可能会用 20% 的时间将想法写成代码，剩下的 80% 时间则用来清理错误和修复漏洞。错误信息绝对是我们每天都会看到的东西。然而，你是否曾经在 Python 错误信息上遇到过困难呢？

例如，错误信息可能非常冗长，这虽然不坏，但很难区分不同的部分，并快速找到我们需要的信息。堆栈跟踪有时也太过庞大和复杂，难以理解。除非我们重写 Exception 类，否则也不容易定制错误信息，而这可能再次令人不知所措。

在本文中，我将介绍一个名为 PrettyError 的库，它可以帮助我们解决上述所有痛点，甚至更多。它有许多酷炫的功能，可以简化我们的调试过程，并帮助我们在编程工作中节省大量时间。

# 1\. 安装与快速入门

![](img/239987fa1bf9951261075d384b2e3ba0.png)

图片来源：[Pexels](https://pixabay.com/users/pexels-2286921/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1841550) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1841550)
