# 使用LangFlow和Ollama构建本地RAG聊天机器人，无需编码

> 原文：[https://towardsdatascience.com/building-local-rag-chatbots-without-coding-using-langflow-and-ollama-60760e8ed086?source=collection_archive---------0-----------------------#2024-04-08](https://towardsdatascience.com/building-local-rag-chatbots-without-coding-using-langflow-and-ollama-60760e8ed086?source=collection_archive---------0-----------------------#2024-04-08)

## 基于LangChain快速原型化RAG应用程序

[](https://medium.com/@yanli.liu?source=post_page---byline--60760e8ed086--------------------------------)[![Yanli Liu](../Images/31342655ab635eb38e3ce501235f1b89.png)](https://medium.com/@yanli.liu?source=post_page---byline--60760e8ed086--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60760e8ed086--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--60760e8ed086--------------------------------) [Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--60760e8ed086--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60760e8ed086--------------------------------) ·阅读时间10分钟·2024年4月8日

--

还记得以前构建智能聊天机器人需要几个月的编码吗？

像LangChain这样的框架无疑简化了开发，但对于非程序员来说，几百行代码仍然是一个障碍。⁤

有没有更简单的方法？（*如果你还不是Medium会员，可以在这里* [*查看完整故事*](https://medium.com/towards-data-science/building-local-rag-chatbots-without-coding-using-langflow-and-ollama-60760e8ed086?sk=680bf6f41e4566b870962f4bc00082bf) *并考虑订阅Medium会员支持作者*) 

![](../Images/717c7dae13fd20f9bbf41ddb9c6c29ad.png)

图片由[Ravi Palwe](https://unsplash.com/@ravipalwe?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

那时我发现了“Lang Flow”，一个基于LangChain Python版本的开源包。它让你无需编写一行代码就能创建AI应用程序。它为你提供了一个画布，你只需拖动组件并将它们连接起来，就能构建你的聊天机器人。

在这篇文章中，我们将使用**LangFlow**在几分钟内构建一个智能AI聊天机器人原型。对于后端，我们将使用**Ollama**进行嵌入模型和大型语言模型，这意味着该应用程序将在本地运行并且免费！最后，我们将把这个流程转化为一个**Streamlit**应用程序，几乎不需要编码。

# 检索增强生成管道简介……
