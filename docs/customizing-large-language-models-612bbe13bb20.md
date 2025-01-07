# 定制大型语言模型

> 原文：[https://towardsdatascience.com/customizing-large-language-models-612bbe13bb20?source=collection_archive---------7-----------------------#2024-03-31](https://towardsdatascience.com/customizing-large-language-models-612bbe13bb20?source=collection_archive---------7-----------------------#2024-03-31)

![](../Images/3dd1ba13bb74102bc6c8f9e9eb40c8a3.png)

图片来源：作者（Ideogram）

## 使用OLLAMA和Modelfile定制、运行并保存LLM

[](https://medium.com/@thomas_reid?source=post_page---byline--612bbe13bb20--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--612bbe13bb20--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--612bbe13bb20--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--612bbe13bb20--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--612bbe13bb20--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--612bbe13bb20--------------------------------) ·阅读时长：11分钟·2024年3月31日

--

在这篇文章中，我将向你展示如何在Ollama中使用Modelfile来更改与现有LLM（Llama2）交互时的行为。我还将展示如何将你新定制的模型保存到Ollama服务器上的个人命名空间。

我知道有很多不同的“**llama**”可能会让人有点困惑。只需记住，Ollama是一家让你能够下载并在本地运行多种不同LLM的公司。而Llama2是Meta（Facebook的拥有者）创建的一个特定LLM。除了这种关系，它们之间没有其他关联。

如果你之前从未听说过Ollama，我建议你查看下面我的文章，我将在文章中详细讲解Ollama是什么，以及如何在你的系统上安装它。

[](https://levelup.gitconnected.com/introduction-to-ollama-part-1-1156f9563b8d?source=post_page-----612bbe13bb20--------------------------------) [## Ollama介绍 — 第1部分

### 使用Ollama在本地运行LLM

levelup.gitconnected.com](https://levelup.gitconnected.com/introduction-to-ollama-part-1-1156f9563b8d?source=post_page-----612bbe13bb20--------------------------------)

## 什么是modelfile？

在Ollama中，`modelfile`是指一个配置文件，用于定义创建和共享模型的蓝图。
