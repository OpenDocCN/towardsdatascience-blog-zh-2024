# 使用 LangFlow 和 Ollama 构建本地 RAG 聊天机器人，无需编码

> 原文：[`towardsdatascience.com/building-local-rag-chatbots-without-coding-using-langflow-and-ollama-60760e8ed086?source=collection_archive---------0-----------------------#2024-04-08`](https://towardsdatascience.com/building-local-rag-chatbots-without-coding-using-langflow-and-ollama-60760e8ed086?source=collection_archive---------0-----------------------#2024-04-08)

## 基于 LangChain 快速原型化 RAG 应用程序

[](https://medium.com/@yanli.liu?source=post_page---byline--60760e8ed086--------------------------------)![Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--60760e8ed086--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60760e8ed086--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60760e8ed086--------------------------------) [Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--60760e8ed086--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60760e8ed086--------------------------------) ·阅读时间 10 分钟·2024 年 4 月 8 日

--

还记得以前构建智能聊天机器人需要几个月的编码吗？

像 LangChain 这样的框架无疑简化了开发，但对于非程序员来说，几百行代码仍然是一个障碍。⁤

有没有更简单的方法？（*如果你还不是 Medium 会员，可以在这里* [*查看完整故事*](https://medium.com/towards-data-science/building-local-rag-chatbots-without-coding-using-langflow-and-ollama-60760e8ed086?sk=680bf6f41e4566b870962f4bc00082bf) *并考虑订阅 Medium 会员支持作者*) 

![](img/717c7dae13fd20f9bbf41ddb9c6c29ad.png)

图片由[Ravi Palwe](https://unsplash.com/@ravipalwe?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

那时我发现了“Lang Flow”，一个基于 LangChain Python 版本的开源包。它让你无需编写一行代码就能创建 AI 应用程序。它为你提供了一个画布，你只需拖动组件并将它们连接起来，就能构建你的聊天机器人。

在这篇文章中，我们将使用**LangFlow**在几分钟内构建一个智能 AI 聊天机器人原型。对于后端，我们将使用**Ollama**进行嵌入模型和大型语言模型，这意味着该应用程序将在本地运行并且免费！最后，我们将把这个流程转化为一个**Streamlit**应用程序，几乎不需要编码。

# 检索增强生成管道简介……
