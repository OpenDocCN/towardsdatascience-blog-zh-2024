# Python 可能是你最好的 PDF 数据提取工具

> 原文：[`towardsdatascience.com/python-might-be-your-best-pdf-data-extractor-f5d42e2634b7?source=collection_archive---------4-----------------------#2024-11-01`](https://towardsdatascience.com/python-might-be-your-best-pdf-data-extractor-f5d42e2634b7?source=collection_archive---------4-----------------------#2024-11-01)

## 一步一步的指南，教你如何在几秒钟内最大化利用冗长的数据报告

[](https://arijoury.medium.com/?source=post_page---byline--f5d42e2634b7--------------------------------)![Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--f5d42e2634b7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f5d42e2634b7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f5d42e2634b7--------------------------------) [Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--f5d42e2634b7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f5d42e2634b7--------------------------------) ·阅读时间：9 分钟·2024 年 11 月 1 日

--

![](img/c42e7603317a87220ce84d21a76a847b.png)

在大型 PDF 文件中找到关键信息是一项挑战，Python 可以帮助解决这个问题。图像由[Leonardo AI](https://app.leonardo.ai/image-generation)生成

可携带文档格式（PDF）自[Adobe](https://www.prepressure.com/pdf/basics/history)于 1990 年代初期推出以来，一直在数字世界中流传。PDF 格式旨在保留不同设备间的格式一致性，因此迅速成为了分享合同、年度报告和复杂财务文件的首选格式。

在金融、法律服务以及许多（如果不是全部）其他行业中，PDF 至今仍然是主流格式。任何人都可以打开 PDF 文件，并且无论使用什么阅读器，它都会以相同的方式显示。这对于不应改变的文件是一种优势——与可编辑的 Word 或 PowerPoint 文件不同。

PDF 的一个缺点是它们是为人眼设计的。换句话说，如果你想处理一个 400 页的报告，你最初可能需要手动打开它，并至少自己滚动到相关部分。当处理存储在 PDF 中的大量数据时，这就成了一个问题。

在如此庞大的文件上训练聊天机器人[仍然具有挑战性](https://community.openai.com/t/using-large-pdfs-to-make-a-chatbot/372228)，更不用说它非常耗能了。即便成功，最先进的聊天机器人在被询问文件内容时，给出的回答也最多是靠不住的。为了使这种聊天机器人更好地适应类型……
