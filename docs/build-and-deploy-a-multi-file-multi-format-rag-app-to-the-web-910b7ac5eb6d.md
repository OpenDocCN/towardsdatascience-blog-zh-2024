# 构建并部署一个多文件、多格式的 RAG 应用到 Web

> 原文：[`towardsdatascience.com/build-and-deploy-a-multi-file-multi-format-rag-app-to-the-web-910b7ac5eb6d?source=collection_archive---------2-----------------------#2024-10-24`](https://towardsdatascience.com/build-and-deploy-a-multi-file-multi-format-rag-app-to-the-web-910b7ac5eb6d?source=collection_archive---------2-----------------------#2024-10-24)

![](img/73c107381cbd02c4aa7182ffb023d1ed.png)

图像由 AI（Dalle-3）生成

## 开发应用

## 第一部分 — 使用 Python、Gradio、GROQ 和 LlamaIndex 开发代码

[](https://medium.com/@thomas_reid?source=post_page---byline--910b7ac5eb6d--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--910b7ac5eb6d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--910b7ac5eb6d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--910b7ac5eb6d--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--910b7ac5eb6d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--910b7ac5eb6d--------------------------------) ·阅读时间 11 分钟·2024 年 10 月 24 日

--

> 这是一个两部分系列文章的第一部分。在这一部分（第一部分），我将向你展示如何开发一个有用的 Web 应用，能够上传和读取多种不同类型的文件，例如 PDF、TXT、DOCX 等等……然后我们将利用 AI 和 RAG 来分析这些文件并回答相关问题。
> 
> 在第二部分，我将向你展示如何使用 Hugging Face Spaces 将你的应用部署到 Web 上，让全世界的人都能惊叹于你的伟大。
> 
> **附言：如果你想提前预览在 Hugging Face Spaces 上部署的应用，请点击这个** [**链接**](https://huggingface.co/spaces/taupirho/gradio_multi_file_rag)

毫无疑问，AI 和大型语言模型的一个重要增长领域是检索增强生成（RAG）领域。RAG 是一种微调方法，你通过为 LLM 提供它在训练数据中无法访问的特定信息。

如果你之前从未听说过 RAG，不用担心，它并不复杂。一个典型的 RAG 流程包括读取一个或多个（通常是 PDF 格式）文档，但它们也可以是 CSV、TXT 或其他格式。将这些文档分割成较小的文本块，对每个 token 进行编码（有点像…）
