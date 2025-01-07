# 如何轻松为你的本地LLM设置一个简洁的用户界面

> 原文：[https://towardsdatascience.com/how-to-easily-set-up-a-neat-user-interface-for-your-local-llm-1a972da2310e?source=collection_archive---------3-----------------------#2024-08-28](https://towardsdatascience.com/how-to-easily-set-up-a-neat-user-interface-for-your-local-llm-1a972da2310e?source=collection_archive---------3-----------------------#2024-08-28)

## 一步步的指南：如何使用Open WebUI在本地运行Llama3

[](https://guillaume-weingertner.medium.com/?source=post_page---byline--1a972da2310e--------------------------------)[![Guillaume Weingertner](../Images/fbfb34af986a7788394b6033c6954d57.png)](https://guillaume-weingertner.medium.com/?source=post_page---byline--1a972da2310e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1a972da2310e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1a972da2310e--------------------------------) [Guillaume Weingertner](https://guillaume-weingertner.medium.com/?source=post_page---byline--1a972da2310e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1a972da2310e--------------------------------) ·阅读时间6分钟·2024年8月28日

--

![](../Images/3b758ee77d1c941a6348673fd2974348.png)

图片由AI（Midjourney）生成，作者提供

# #1 为什么选择本地LLM

无论是由于公司限制，还是希望安全地处理个人数据，许多人因为数据隐私问题避免使用ChatGPT。

幸运的是，有一些解决方案可以让你**无限制**地使用LLM，而不需要将敏感数据发送到云端。

在我之前的文章中，我探讨了通过Ollama在本地运行Llama 3的一种解决方案。

[](/running-local-llms-is-more-useful-and-easier-than-you-think-f735631272ad?source=post_page-----1a972da2310e--------------------------------) [## 本地运行LLM比你想象的更有用、更容易

### 一步步的指南：如何使用Python在本地运行Llama3

towardsdatascience.com](/running-local-llms-is-more-useful-and-easier-than-you-think-f735631272ad?source=post_page-----1a972da2310e--------------------------------)

**前提条件**：在上一篇文章的结尾，我们已经通过Ollama在本地运行了Llama 3，并且可以通过终端或在Jupyter Notebook中使用它。

> 在本文中，我将解释如何通过简洁的用户界面在几分钟内使本地LLM的使用变得更加用户友好！
