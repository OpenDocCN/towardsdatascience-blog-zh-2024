# 构建并将多文件 RAG 应用部署到 Web

> 原文：[`towardsdatascience.com/build-and-deploy-a-multi-file-rag-app-to-the-web-70ee4eceb0e3?source=collection_archive---------5-----------------------#2024-11-01`](https://towardsdatascience.com/build-and-deploy-a-multi-file-rag-app-to-the-web-70ee4eceb0e3?source=collection_archive---------5-----------------------#2024-11-01)

![](img/91f82c175d18f1ac2468a080f7e79b83.png)

图片来自 AI (Dalle-3)

## 第二部分 — 使用 Hugging Face Spaces 将应用部署到网络

[](https://medium.com/@thomas_reid?source=post_page---byline--70ee4eceb0e3--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--70ee4eceb0e3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--70ee4eceb0e3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--70ee4eceb0e3--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--70ee4eceb0e3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--70ee4eceb0e3--------------------------------) ·阅读时间：9 分钟·2024 年 11 月 1 日

--

> 这是关于构建和部署基于 Gradio 的 AI 网络应用的两部分系列文章中的第二部分。
> 
> 本部分内容将讲解如何使用 Hugging Face Spaces 将已完成的应用部署到全球互联网。
> 
> **PS. 如果你想提前预览已部署的应用，可以点击这个** [**链接**](https://huggingface.co/spaces/taupirho/gradio_multi_file_rag)

我之前在许多文章中提到过 Gradio。依我看，它是构建 Python 代码 GUI 应用最简单的方法之一。

如果 Gradio 对你来说完全陌生，或者你只是略有了解，我建议你查看我下面的文章，在其中我介绍了他们是谁以及他们在做什么。我还展示了一些小的代码示例，演示了 Gradio 的实际应用。

[](https://ai.gopubby.com/gradio-rapid-gui-prototyping-a0091c28116b?source=post_page-----70ee4eceb0e3--------------------------------) [## Gradio：快速 GUI 原型设计

### 使用 Python 在几分钟内创建直观的 Web 界面

ai.gopubby.com](https://ai.gopubby.com/gradio-rapid-gui-prototyping-a0091c28116b?source=post_page-----70ee4eceb0e3--------------------------------)

在上一篇文章中，我带你了解了如何构建一个多文件 RAG 聊天应用，该应用能够上传、读取和分析各种文档格式，包括 PDF、文本、Microsoft Word 和 Excel 文件……
