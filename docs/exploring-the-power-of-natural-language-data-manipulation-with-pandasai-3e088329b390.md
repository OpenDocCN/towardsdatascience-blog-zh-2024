# 探索使用PandasAI进行自然语言数据处理的强大功能

> 原文：[https://towardsdatascience.com/exploring-the-power-of-natural-language-data-manipulation-with-pandasai-3e088329b390?source=collection_archive---------5-----------------------#2024-04-15](https://towardsdatascience.com/exploring-the-power-of-natural-language-data-manipulation-with-pandasai-3e088329b390?source=collection_archive---------5-----------------------#2024-04-15)

## 生成式AI如何增强Pandas的功能

[](https://medium.com/@mia_dwyer?source=post_page---byline--3e088329b390--------------------------------)[![Mia Dwyer](../Images/e3b6899877e5cc5e4961a6d1dd6954d6.png)](https://medium.com/@mia_dwyer?source=post_page---byline--3e088329b390--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3e088329b390--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3e088329b390--------------------------------) [Mia Dwyer](https://medium.com/@mia_dwyer?source=post_page---byline--3e088329b390--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3e088329b390--------------------------------) ·阅读时间5分钟·2024年4月15日

--

我最近发现了一个非常有趣的新工具，它结合了我对数据科学和生成式AI的兴趣，允许你在Jupyter笔记本中直接使用自然语言与数据进行“对话”，借助ChatGPT。

进入[PandasAI](https://docs.pandas-ai.com/en/latest/) 🐼✨一个免费的开源Python库。*

![](../Images/4f7f5917b2bb65af264521f437338ed7.png)

该图像由我使用DALL-E创建

# 什么是Pandas AI？

Datacamp将[PandasAI](https://docs.pandas-ai.com/en/latest/)描述为“一款利用生成式AI模型增强Pandas功能的Python库。它旨在补充Pandas库，这是一个广泛使用的数据分析和处理工具。”[[1](https://www.datacamp.com/blog/an-introduction-to-pandas-ai)]

以下是如何利用PandasAI强大功能的示例，即使对Python和Pandas了解有限：

![](../Images/fe3e08be85d0b6e2950743afd090e258.png)

该图像由作者在Jupyter Lab中创建，使用pandasai与交易数据集进行“对话”。

# 如何进行设置

这一部分确实需要注册OpenAI开发者账号，但不用担心！OpenAI使这个步骤变得非常简单，主要只需要生成一个令牌，并将其包含在你的笔记本中。
