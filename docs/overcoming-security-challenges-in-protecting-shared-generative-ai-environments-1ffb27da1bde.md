# 克服保护共享生成式AI环境中的安全挑战

> 原文：[https://towardsdatascience.com/overcoming-security-challenges-in-protecting-shared-generative-ai-environments-1ffb27da1bde?source=collection_archive---------4-----------------------#2024-12-02](https://towardsdatascience.com/overcoming-security-challenges-in-protecting-shared-generative-ai-environments-1ffb27da1bde?source=collection_archive---------4-----------------------#2024-12-02)

## 确保在**多租户**中的安全AI

[](https://medium.com/@han.heloir?source=post_page---byline--1ffb27da1bde--------------------------------)[![Han HELOIR, Ph.D. ☕️](../Images/53c132b64fda2f1d9ebd6af6d582d24c.png)](https://medium.com/@han.heloir?source=post_page---byline--1ffb27da1bde--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ffb27da1bde--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1ffb27da1bde--------------------------------) [Han HELOIR, Ph.D. ☕️](https://medium.com/@han.heloir?source=post_page---byline--1ffb27da1bde--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ffb27da1bde--------------------------------) ·阅读时长13分钟·2024年12月2日

--

[免费链接](/overcoming-security-challenges-in-protecting-shared-generative-ai-environments-1ffb27da1bde?sk=859dd0b0d33e5567b01596d873a4dfc3) => 请帮助点赞 [这篇LinkedIn帖子](https://www.linkedin.com/posts/hanheloiryan_overcoming-security-challenges-in-protecting-activity-7269387661981757443-MrZm?utm_source=share&utm_medium=member_desktop) 来传播本文。

# **引言**

让我们首先概述一下该领域的情况：许多组织正在搭乘生成式AI的浪潮。[一份最新报告](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai)显示，超过65%的组织报告称他们已经将生成式AI应用于其业务流程。然而，经过更深入的检查发现，绝大多数这些应用要么处于初期阶段，要么处于概念设计阶段，这主要是由于公司对成功部署它们的能力存在过度乐观的偏见。

![](../Images/196a261e480cc8bc197554141979e016.png)

图片来源：[Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

概念与生产之间的差距源于几个挑战：数据集成问题、遗留系统的限制、使用案例的投资回报考虑，以及安全壁垒。在本文中，我们将重点讨论一个关键的安全问题——**多租户中的资源**。

在生成式人工智能驱动的应用程序中，通常不仅仅是关于撰写文本或回复。大多数应用程序会执行数据查找操作，向大型语言模型（LLM）提供相关信息，以确保输出质量。当一个人工智能模型应用于针对多个客户或内部部门的生成式人工智能时，我们通常会让每个客户或部门拥有不同的…
