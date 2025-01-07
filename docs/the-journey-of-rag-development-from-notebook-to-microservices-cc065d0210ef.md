# RAG 开发之旅：从笔记本到微服务

> 原文：[`towardsdatascience.com/the-journey-of-rag-development-from-notebook-to-microservices-cc065d0210ef?source=collection_archive---------2-----------------------#2024-02-21`](https://towardsdatascience.com/the-journey-of-rag-development-from-notebook-to-microservices-cc065d0210ef?source=collection_archive---------2-----------------------#2024-02-21)

## 将 Colab 笔记本转换为支持 Milvus 和 NeMo Guardrails 的两个微服务

[](https://medium.com/@wenqiglantz?source=post_page---byline--cc065d0210ef--------------------------------)![Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--cc065d0210ef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cc065d0210ef--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cc065d0210ef--------------------------------) [Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--cc065d0210ef--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cc065d0210ef--------------------------------) ·阅读时间：11 分钟·2024 年 2 月 21 日

--

![](img/f810b68ec0b025724d0f45befe4b6cdb.png)

图像由作者通过 DALL-E 3 生成

在探索企业级 RAG 的过程中，本文介绍了如何从在 Colab 笔记本中开发的 RAG 管道 POC 中创建 RAG 微服务。我们采取了以下方法：

+   使用 LlamaIndex 的`create-llama`命令行工具生成 RAG 微服务的模板代码。

+   开发两个微服务：`ingestion-service`和`inference-service`，覆盖 RAG 的两个主要阶段。

+   将 Colab 笔记本中的代码逻辑转换为微服务。

+   将 Milvus 向量数据库集成到我们的新微服务中。

+   将 NeMo Guardrails 添加到`inference-service`，为用户输入、LLM 输出、主题审核和自定义操作提供保护机制，并与 LlamaIndex 集成。

# 笔记本

为了快速原型开发，Colab 笔记本因其易用性、可访问性和免费使用而成为理想选择。

例如，[这个 Colab 笔记本](https://colab.research.google.com/drive/1EvyAIflOtyAwNbtghNv0i2asSHuPV-VN?usp=sharing)演示了如何在 RAG 管道中使用元数据替换+节点句子窗口，该管道用作[NVIDIA AI 企业用户指南](https://docs.nvidia.com/ai-enterprise/latest/pdf/nvidia-ai-enterprise-user-guide.pdf)的聊天机器人。
