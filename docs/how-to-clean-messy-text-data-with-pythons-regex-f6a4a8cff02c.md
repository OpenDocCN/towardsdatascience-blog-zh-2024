# 如何使用 Python 的正则表达式清理杂乱的文本数据

> 原文：[`towardsdatascience.com/how-to-clean-messy-text-data-with-pythons-regex-f6a4a8cff02c?source=collection_archive---------6-----------------------#2024-11-06`](https://towardsdatascience.com/how-to-clean-messy-text-data-with-pythons-regex-f6a4a8cff02c?source=collection_archive---------6-----------------------#2024-11-06)

## 如何使用正则表达式将半结构化文本转化为可用数据

[](https://arijoury.medium.com/?source=post_page---byline--f6a4a8cff02c--------------------------------)![Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--f6a4a8cff02c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f6a4a8cff02c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6a4a8cff02c--------------------------------) [Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--f6a4a8cff02c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6a4a8cff02c--------------------------------) ·9 分钟阅读·2024 年 11 月 6 日

--

![](img/ac0932f20a14bb4e33a9c07f8d4a07f0.png)

我们可以通过 Python 对杂乱的数据进行筛查，寻找正则表达式。图片由[Leonardo AI](https://app.leonardo.ai/image-generation)生成。

想象一下：你被分配了分析一份包含文本和表格的冗长 PDF 报告中的数值数据的任务。一位同事已经使用光学字符识别技术提取了信息（见上周的文章）。

不幸的是，这个文件并不是一个结构化的数据集，而是相当杂乱——你会发现多余的标题、冗余的脚注和不规则的换行符。数字格式不一致，数据描述散布各处，这使得没有经过大量预处理就几乎无法进行有意义的分析。看起来，今天你将面对数小时的繁琐数据清理工作。

幸运的是，你已经发现了正则表达式。正则表达式（Regex）是用于文本模式匹配的强大工具。它听起来简单，但允许用户定义、搜索并操作文本中的特定模式，这使得它在处理杂乱数据时非常有效。

本文将为你提供一些关于正则表达式的背景知识，并介绍它在 Python 中的实现。接着我们会深入探讨用于数据清理的正则表达式的关键功能，并提供一个实际操作的示例（这是我们最近在 Wangari 遇到的），以说明这在实践中的应用。如果你…
