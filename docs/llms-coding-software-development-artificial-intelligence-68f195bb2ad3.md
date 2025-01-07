# 为什么 LLMs 不适合编码 — 第二部分

> 原文：[`towardsdatascience.com/llms-coding-software-development-artificial-intelligence-68f195bb2ad3?source=collection_archive---------1-----------------------#2024-05-20`](https://towardsdatascience.com/llms-coding-software-development-artificial-intelligence-68f195bb2ad3?source=collection_archive---------1-----------------------#2024-05-20)

## 用于编码任务的大型语言模型

[](https://medium.com/@andvalenzuela?source=post_page---byline--68f195bb2ad3--------------------------------)![Andrea Valenzuela](https://medium.com/@andvalenzuela?source=post_page---byline--68f195bb2ad3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--68f195bb2ad3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68f195bb2ad3--------------------------------) [Andrea Valenzuela](https://medium.com/@andvalenzuela?source=post_page---byline--68f195bb2ad3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68f195bb2ad3--------------------------------) ·6 分钟阅读·2024 年 5 月 20 日

--

![](img/591bff2e771516cb8f20a5bc9481bda2.png)

自制图像。

在发布这篇系列文章的第一篇“[为什么 LLMs 不适合编码](https://medium.com/towards-data-science/llms-coding-chatgpt-python-artificial-intelligence-4ea7a7bbdd93)”后，我在社交媒体上收到了几条评论，例如：

> “我在用 ChatGPT 进行编码，它工作得非常好。”
> 
> “你错了。大型语言模型是有用的编码助手。”

我对这些反应感到惊讶，因为这篇文章系列的目的并不是劝阻任何人使用大型语言模型（LLMs）进行编码，而是**识别需要改进的关键领域，以将 LLMs 转变为更有效的编码助手。**

尽管像 ChatGPT 这样的 LLMs 在某些情况下可能有帮助，**它们经常生成语法正确但不够优化**甚至在功能上不正确的代码。

在上一篇文章中，我们讨论了标记器、在应用于代码时上下文窗口的复杂性以及训练本身的性质如何影响这些模型在编码任务中的表现。

在第二篇文章中，我们将更深入地探讨这些模型在进行编码任务时所接受的训练类型，以及**LLMs 在“开箱即用”时天生不擅长编码的另一个原因：保持更新的挑战**…
