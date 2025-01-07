# 构建你自己的AI群聊：一段进入定制宇宙与角色的旅程

> 原文：[https://towardsdatascience.com/building-your-own-ai-group-chat-a-journey-into-custom-universes-and-characters-73634ee0253b?source=collection_archive---------10-----------------------#2024-10-29](https://towardsdatascience.com/building-your-own-ai-group-chat-a-journey-into-custom-universes-and-characters-73634ee0253b?source=collection_archive---------10-----------------------#2024-10-29)

## 如何使用Ollama、FastAPI、开源LLMs和React创建沉浸式AI群聊

[](https://medium.com/@maximejabarian?source=post_page---byline--73634ee0253b--------------------------------)[![Maxime Jabarian](../Images/d6c2198e2e3259ae98b5bbe0e3079768.png)](https://medium.com/@maximejabarian?source=post_page---byline--73634ee0253b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--73634ee0253b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--73634ee0253b--------------------------------) [Maxime Jabarian](https://medium.com/@maximejabarian?source=post_page---byline--73634ee0253b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--73634ee0253b--------------------------------) ·9分钟阅读·2024年10月29日

--

![](../Images/eaf5532a5fedf926e7f032fdc6c184d7.png)

[来源](https://unsplash.com/fr/photos/personne-tenant-un-sabre-laser-rouge-EpIUbeFrqwQ)

> 欢迎来到本文，在这里我将展示如何创建一个沉浸式AI群聊。作为一名LLM工程师，我并不满足于到处可见的典型一对一AI聊天——因此在业余时间，我着手创建一个AI群聊，用户可以在任何想象中的宇宙中与**多个角色同时互动**，并且无需花费任何费用。

如果你不是会员，[**点击这里阅读**](https://medium.com/towards-data-science/building-your-own-ai-group-chat-a-journey-into-custom-universes-and-characters-73634ee0253b?sk=fceb7066fc7fa6ff5c64122d3cdb9217)**.**

在第一部分中，我将带你了解如何与多个角色同时互动。接着，我们将探索我使用的工具——Python、Ollama、React、FastAPI和开源大型语言模型（LLMs）。最后，你将获得所有构建自己定制宇宙和角色的技巧与见解，并以我的代码为起点，同时提供一些如何改进它的思路（因为这是一个有趣的副项目）。

# 为什么要构建AI群聊？

![](../Images/4b901925240c040efe6c7345468b3d71.png)

图片由作者提供
