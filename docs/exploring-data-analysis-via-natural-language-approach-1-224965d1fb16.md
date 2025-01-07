# 通过自然语言探索数据分析——方法1

> 原文：[https://towardsdatascience.com/exploring-data-analysis-via-natural-language-approach-1-224965d1fb16?source=collection_archive---------4-----------------------#2024-01-17](https://towardsdatascience.com/exploring-data-analysis-via-natural-language-approach-1-224965d1fb16?source=collection_archive---------4-----------------------#2024-01-17)

## 新系列测试大型语言模型是否以及如何将关于数据集的问题转化为能够实时运行的代码，以进行分析，所有操作都在网络和客户端端执行。

[](https://lucianosphere.medium.com/?source=post_page---byline--224965d1fb16--------------------------------)[![LucianoSphere (Luciano Abriata, PhD)](../Images/a8ae3085d094749bbdd1169cca672b86.png)](https://lucianosphere.medium.com/?source=post_page---byline--224965d1fb16--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--224965d1fb16--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--224965d1fb16--------------------------------) [LucianoSphere (Luciano Abriata, PhD)](https://lucianosphere.medium.com/?source=post_page---byline--224965d1fb16--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--224965d1fb16--------------------------------) ·27分钟阅读·2024年1月17日

--

![](../Images/1a960ab99e11d7bb7b56c08103f50bc2.png)

图片通过Dall-E-3生成，使用Skype/Bing进行解释，详细信息请见[此处](https://medium.com/ai-in-plain-english/like-chatgpt-but-with-web-search-and-image-generation-capabilities-and-free-on-your-skype-b06388dd8c76)。

大型语言模型（LLMs）在众多应用中已经证明其极大的能力，其中一些功能甚至在设计之初并未预料到，研究人员（以及普通用户）不断发掘新的功能——当然，也有局限性。但自从GPT-3大约两年前开始提供编程应用以来，我一直有一个关于LLM的迫切问题：

> LLM能否以某种方式帮助数据分析，使用户能够专注于科学或工程问题，而不需要关注任何特定的数学、算法或编程技能？

或者换个更个人化、直接的方式表达：

> 我可以用自己的语言向LLM提问一个数据集问题，并让它通过数学或脚本来解释这些问题并给出答案吗？
