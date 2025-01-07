# 使用LayoutLM和Label Studio解析您的发票

> 原文：[https://towardsdatascience.com/parse-your-invoices-with-layoutlm-and-label-studio-6055ce3c14fe?source=collection_archive---------1-----------------------#2024-04-16](https://towardsdatascience.com/parse-your-invoices-with-layoutlm-and-label-studio-6055ce3c14fe?source=collection_archive---------1-----------------------#2024-04-16)

## 使用Transformers库、Label Studio和AWS S3对您的发票进行LayoutLM的微调。

[](https://medium.com/@jeremyarancio?source=post_page---byline--6055ce3c14fe--------------------------------)[![Jeremy Arancio](../Images/37c4c41e71eb91cfffc7e4ff2bb4394a.png)](https://medium.com/@jeremyarancio?source=post_page---byline--6055ce3c14fe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6055ce3c14fe--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6055ce3c14fe--------------------------------) [Jeremy Arancio](https://medium.com/@jeremyarancio?source=post_page---byline--6055ce3c14fe--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6055ce3c14fe--------------------------------) ·阅读时长34分钟·2024年4月16日

--

从发票中提取信息长期以来一直是公司、机构和会计人员重复且繁琐的任务。

这个任务可以自动化吗？答案是肯定的。

这就是机器学习的承诺：处理成千上万的文档并提取所有相关信息。

许多公司，如[Rossum](https://rossum.ai/)、[Digitoo](https://www.digitoo.cz/)或[Docsumo](https://www.docsumo.com/)，都是基于这一简单想法创建的，并且累计筹集了[数亿美元](https://rossum.ai/blog/rossum-raises-record-100-million-series-a-from-general-catalyst-to-reinvent-b2b-document-communication/)，证明了这种技术的需求。

您也可以创建自己的解决方案。

在本文中，我将引导您通过构建一个微调您公司文档的发票解析器的过程。

我们介绍**LayoutLM**，这是由微软开发的一个著名模型，用于从文档中提取信息。为了定制适合我们特定需求的解决方案，我们使用**Label Studio**进行文档标注，这是一款开源标注工具，并将其与我们的远程存储**AWS S3**连接。

让我们开始吧！

![](../Images/663af8bdda741cb606e90c82ef79bb0f.png)

使用Label Studio进行发票标注，以便进行LayoutLM训练（图片来自作者）

# LayoutLM：文档图像理解的布局
