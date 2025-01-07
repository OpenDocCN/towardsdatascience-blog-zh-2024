# 构建并将多文件RAG应用部署到Web

> 原文：[https://towardsdatascience.com/build-and-deploy-a-multi-file-rag-app-to-the-web-70ee4eceb0e3?source=collection_archive---------5-----------------------#2024-11-01](https://towardsdatascience.com/build-and-deploy-a-multi-file-rag-app-to-the-web-70ee4eceb0e3?source=collection_archive---------5-----------------------#2024-11-01)

![](../Images/91f82c175d18f1ac2468a080f7e79b83.png)

图片来自AI (Dalle-3)

## 第2部分 — 使用Hugging Face Spaces将应用部署到网络

[](https://medium.com/@thomas_reid?source=post_page---byline--70ee4eceb0e3--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--70ee4eceb0e3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--70ee4eceb0e3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--70ee4eceb0e3--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--70ee4eceb0e3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--70ee4eceb0e3--------------------------------) ·阅读时间：9分钟·2024年11月1日

--

> 这是关于构建和部署基于Gradio的AI网络应用的两部分系列文章中的第二部分。
> 
> 本部分内容将讲解如何使用Hugging Face Spaces将已完成的应用部署到全球互联网。
> 
> **PS. 如果你想提前预览已部署的应用，可以点击这个** [**链接**](https://huggingface.co/spaces/taupirho/gradio_multi_file_rag)

我之前在许多文章中提到过Gradio。依我看，它是构建Python代码GUI应用最简单的方法之一。

如果Gradio对你来说完全陌生，或者你只是略有了解，我建议你查看我下面的文章，在其中我介绍了他们是谁以及他们在做什么。我还展示了一些小的代码示例，演示了Gradio的实际应用。

[](https://ai.gopubby.com/gradio-rapid-gui-prototyping-a0091c28116b?source=post_page-----70ee4eceb0e3--------------------------------) [## Gradio：快速GUI原型设计

### 使用Python在几分钟内创建直观的Web界面

ai.gopubby.com](https://ai.gopubby.com/gradio-rapid-gui-prototyping-a0091c28116b?source=post_page-----70ee4eceb0e3--------------------------------)

在上一篇文章中，我带你了解了如何构建一个多文件RAG聊天应用，该应用能够上传、读取和分析各种文档格式，包括PDF、文本、Microsoft Word和Excel文件……
