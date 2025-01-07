# 如何通过 RAG 提高 LLMs

> 原文：[`towardsdatascience.com/how-to-improve-llms-with-rag-abdc132f76ac?source=collection_archive---------0-----------------------#2024-03-09`](https://towardsdatascience.com/how-to-improve-llms-with-rag-abdc132f76ac?source=collection_archive---------0-----------------------#2024-03-09)

## 面向初学者的介绍，附带 Python 代码

[](https://shawhin.medium.com/?source=post_page---byline--abdc132f76ac--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--abdc132f76ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--abdc132f76ac--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abdc132f76ac--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--abdc132f76ac--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abdc132f76ac--------------------------------) ·13 分钟阅读·2024 年 3 月 9 日

--

本文是关于在实践中使用大型语言模型（LLMs）[更大系列的一部分](https://shawhin.medium.com/list/large-language-models-llms-8e009ae3054c)。在上一篇文章中，我们使用 QLoRA 微调了 Mistral-7b-Instruct，以回应 YouTube 评论。尽管经过微调的模型在回应观众反馈时成功捕捉了我的风格，但它在回答技术性问题时并没有完全符合我的解释。在这里，我将讨论如何通过检索增强生成（即 RAG）来提高 LLM 的表现。

![](img/324d0e1a0754979b87fd7e0109c25717.png)

原始的 RAG 系统。图像来源于 Canva。

大型语言模型（LLMs）展示了在回应用户查询时存储和调度海量知识的令人印象深刻的能力。这使得像 ChatGPT 这样的强大 AI 系统的诞生成为可能。然而，以这种方式压缩世界知识存在**两个主要限制**。

**首先**，LLM 的知识是静态的，也就是说，随着新信息的出现，它不会更新。**其次**，LLM 可能对在其训练数据中不突出的小众和专业信息理解不足。这些限制可能导致模型对用户查询的反应不理想（甚至是虚构的）。

我们可以通过**利用一个专门的、可变的知识库**来缓解这些限制，例如，客户常见问题、软件文档或产品目录。这可以帮助创建更多…
