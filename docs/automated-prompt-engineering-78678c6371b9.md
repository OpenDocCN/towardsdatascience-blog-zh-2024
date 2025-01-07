# 自动化提示工程

> 原文：[https://towardsdatascience.com/automated-prompt-engineering-78678c6371b9?source=collection_archive---------0-----------------------#2024-03-10](https://towardsdatascience.com/automated-prompt-engineering-78678c6371b9?source=collection_archive---------0-----------------------#2024-03-10)

## 一些反思、文献综述以及关于大型语言模型自动化提示工程的实验

[](https://ianhojy.medium.com/?source=post_page---byline--78678c6371b9--------------------------------)[![Ian Ho](../Images/1b56c25ee3bedfb5c7369d4bfc93aa91.png)](https://ianhojy.medium.com/?source=post_page---byline--78678c6371b9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--78678c6371b9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--78678c6371b9--------------------------------) [Ian Ho](https://ianhojy.medium.com/?source=post_page---byline--78678c6371b9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--78678c6371b9--------------------------------) ·13分钟阅读·2024年3月10日

--

![](../Images/96397b85e75ec0cd1eb75dd91e92a951.png)

图片由作者在DALL-E的帮助下生成

在过去几个月里，我一直在尝试构建各种基于大型语言模型的应用，事实上，花费了大量时间专门用于改进提示词，以便从大型语言模型中获得我想要的输出。

许多时候，我都会陷入一种存在性的空虚，问自己是否只是一个被美化的提示工程师。考虑到当前与大型语言模型（LLM）的互动状态，我仍然倾向于得出“还不是”的结论，大多数晚上，我能克服我的冒充者综合症。今天不打算深入讨论这个话题。

但我仍然经常想，如果有一天，编写提示的过程能被大部分自动化掉，那会是什么样子。我认为，这个未来场景的答案取决于揭示提示工程的本质。

尽管互联网上有无数关于提示工程的手册，我仍然无法决定提示工程是**艺术还是科学**。

一方面，当我根据输出的结果不断学习和编辑我的提示词时，感觉这像是一种艺术。随着时间的推移，你会意识到一些细节很重要——比如用‘must’而不是‘should’……
