# 如何在 SQL 中使用 OpenAI ChatGPT API

> 原文：[`towardsdatascience.com/how-to-use-openai-chatgpt-api-in-sql-9b60d2526a9e?source=collection_archive---------1-----------------------#2024-04-04`](https://towardsdatascience.com/how-to-use-openai-chatgpt-api-in-sql-9b60d2526a9e?source=collection_archive---------1-----------------------#2024-04-04)

## SQL 是否会成为你构建 AI 应用的下一个选择？

[](https://medium.com/@radecicdario?source=post_page---byline--9b60d2526a9e--------------------------------)![Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--9b60d2526a9e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9b60d2526a9e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b60d2526a9e--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--9b60d2526a9e--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b60d2526a9e--------------------------------) ·5 分钟阅读·2024 年 4 月 4 日

--

![](img/90f69d9c03b7c6edd2d06a84a2da8001.png)

图片由[Resource Database](https://unsplash.com/@resourcedatabase?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

提到 ChatGPT 和使用 OpenAI API 时，SQL 并不是首先想到的语言。但它应该成为首选——它是**数据的语言**，而且你可以从 SQL 中发送 HTTP 请求，这为你打开了无限的可能性。

本文将展示如何使用 PL/SQL 编写自定义的 Oracle SQL 函数。该函数将接收一个问题字符串并返回格式化后的 JSON。Oracle 的`dbms_cloud`包将承担大部分繁重的工作，因为它负责发起 API 调用。如果你使用的是其他数据库供应商，我相信你可以找到相应的包和函数集来完成这项工作。

让我们先来看看你需要的前置条件，确保你能跟上。

# SQL 中的 ChatGPT — 前置条件

正如在介绍中所提到的，我正在使用运行在**Autonomous Database 21c**上的 Oracle SQL，该数据库实例是在[Oracle Cloud](https://www.oracle.com/cloud/)中提供的免费实例。如果你想跟着操作，注册一个免费账户，创建数据库实例，并下载连接钱包。

另一个你需要的东西是[OpenAI API 密钥](https://www.maisieai.com/help/how-to-get-an-openai-api-key-for-chatgpt)。链接中的文章展示了如何在几分钟内获取一个密钥。
