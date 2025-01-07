# 文本到 SQL 的 LLM 应用：提示注入

> 原文：[`towardsdatascience.com/text-to-sql-llm-applications-prompt-injections-ebee495d0c16?source=collection_archive---------2-----------------------#2024-02-02`](https://towardsdatascience.com/text-to-sql-llm-applications-prompt-injections-ebee495d0c16?source=collection_archive---------2-----------------------#2024-02-02)

## 了解你的文本到 SQL LLM 应用如何可能受到提示注入的影响，以及你可以采取的保护数据的缓解措施

[](https://ianhojy.medium.com/?source=post_page---byline--ebee495d0c16--------------------------------)![Ian Ho](https://ianhojy.medium.com/?source=post_page---byline--ebee495d0c16--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ebee495d0c16--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebee495d0c16--------------------------------) [Ian Ho](https://ianhojy.medium.com/?source=post_page---byline--ebee495d0c16--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebee495d0c16--------------------------------) ·8 分钟阅读·2024 年 2 月 2 日

--

🔒 [**非会员文章访问**](https://medium.com/towards-data-science/text-to-sql-llm-applications-prompt-injections-ebee495d0c16?sk=b314e7e23087126171c1e51cde02a640)

![](img/8a2bb45c40409805973aff1fda6f2522.png)

作者照片，协助来自 Dall-E-3

# 介绍

最近 LLM（大语言模型）使用的激增为提高我们的工作效率和生产力开辟了许多可能性。其中一个特别令人兴奋的应用是通过基于 LLM 的文本到 SQL 应用程序实现数据分析的民主化。在过去的几个月里，我们看到许多工具相继出现，允许开发者利用 LLM 来实现这一目的，比如 [LangChain SQL 代理工具包](https://python.langchain.com/docs/integrations/toolkits/sql_database) 和最近的 [Vanna AI](https://vanna.ai/)。

别误会，我认为这些工具对于那些希望在决策过程中更加*数据驱动*的团队和组织非常有用。但这些工具提供的抽象化简便性带来了一个关键的安全隐患。当你使用这些模块来构建应用时，你失去了对数据库是否真正安全的可见性，也无法精确控制正在执行的查询。而这在面对提示注入的脆弱性时，尤其令人担忧。
