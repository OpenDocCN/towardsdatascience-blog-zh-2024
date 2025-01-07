# 使用 OpenAI 强大的新 Assistants API 进行数据分析

> 原文：[https://towardsdatascience.com/how-to-use-the-powerful-new-assistants-api-for-data-analysis-c9ea1cab0b53?source=collection_archive---------2-----------------------#2024-01-18](https://towardsdatascience.com/how-to-use-the-powerful-new-assistants-api-for-data-analysis-c9ea1cab0b53?source=collection_archive---------2-----------------------#2024-01-18)

## OpenAI 的 Assistants API* 让我们能够创建 AI 助手，利用能够处理用户提供数据的工具

[](https://medium.com/@alan-jones?source=post_page---byline--c9ea1cab0b53--------------------------------)[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--c9ea1cab0b53--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c9ea1cab0b53--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c9ea1cab0b53--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--c9ea1cab0b53--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c9ea1cab0b53--------------------------------) ·18 分钟阅读·2024年1月18日

--

![](../Images/799acfc9e99b4c75891ff1ec6784b01f.png)

图片由[Tigran Hambardzumyan](https://unsplash.com/@tigranh47?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*请注意，Assistants API 是一个测试版发布，可能会发生变化。因此，本文中的代码可能会随着 API 新版本的发布而过时。本文中的代码是基于 1.6.1 版本构建的*

> “Assistants API 允许你在自己的应用程序中构建 AI 助手。一个助手有指令，可以利用模型、工具和知识来回应用户查询。”——OpenAI

听起来很棒，所以我们将看看如何使用这个新的 API* 对本地数据进行数据分析。

Assistants API 代表了一种替代至少部分 Retrieval Augmented Generation (RAG) 使用方式的方法。那么，RAG是否只是一个过渡措施，当前大语言模型（LLM）缺点的临时解决方案呢？毕竟，LlamaIndex 的 Jerry Liu 曾表示，[RAG 只是一个黑客式解决方案](https://www.latent.space/p/llamaindex)（尽管它是一个强大的解决方案）。

下面是 RAG 当前解决的 LLM 固有的三个具体问题…
