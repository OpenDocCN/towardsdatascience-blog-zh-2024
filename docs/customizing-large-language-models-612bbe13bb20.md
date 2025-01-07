# 定制大型语言模型

> 原文：[`towardsdatascience.com/customizing-large-language-models-612bbe13bb20?source=collection_archive---------7-----------------------#2024-03-31`](https://towardsdatascience.com/customizing-large-language-models-612bbe13bb20?source=collection_archive---------7-----------------------#2024-03-31)

![](img/3dd1ba13bb74102bc6c8f9e9eb40c8a3.png)

图片来源：作者（Ideogram）

## 使用 OLLAMA 和 Modelfile 定制、运行并保存 LLM

[](https://medium.com/@thomas_reid?source=post_page---byline--612bbe13bb20--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--612bbe13bb20--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--612bbe13bb20--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--612bbe13bb20--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--612bbe13bb20--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--612bbe13bb20--------------------------------) ·阅读时长：11 分钟·2024 年 3 月 31 日

--

在这篇文章中，我将向你展示如何在 Ollama 中使用 Modelfile 来更改与现有 LLM（Llama2）交互时的行为。我还将展示如何将你新定制的模型保存到 Ollama 服务器上的个人命名空间。

我知道有很多不同的“**llama**”可能会让人有点困惑。只需记住，Ollama 是一家让你能够下载并在本地运行多种不同 LLM 的公司。而 Llama2 是 Meta（Facebook 的拥有者）创建的一个特定 LLM。除了这种关系，它们之间没有其他关联。

如果你之前从未听说过 Ollama，我建议你查看下面我的文章，我将在文章中详细讲解 Ollama 是什么，以及如何在你的系统上安装它。

[](https://levelup.gitconnected.com/introduction-to-ollama-part-1-1156f9563b8d?source=post_page-----612bbe13bb20--------------------------------) [## Ollama 介绍 — 第一部分

### 使用 Ollama 在本地运行 LLM

levelup.gitconnected.com](https://levelup.gitconnected.com/introduction-to-ollama-part-1-1156f9563b8d?source=post_page-----612bbe13bb20--------------------------------)

## 什么是 modelfile？

在 Ollama 中，`modelfile`是指一个配置文件，用于定义创建和共享模型的蓝图。
