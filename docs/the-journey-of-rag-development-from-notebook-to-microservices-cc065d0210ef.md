# RAG开发之旅：从笔记本到微服务

> 原文：[https://towardsdatascience.com/the-journey-of-rag-development-from-notebook-to-microservices-cc065d0210ef?source=collection_archive---------2-----------------------#2024-02-21](https://towardsdatascience.com/the-journey-of-rag-development-from-notebook-to-microservices-cc065d0210ef?source=collection_archive---------2-----------------------#2024-02-21)

## 将Colab笔记本转换为支持Milvus和NeMo Guardrails的两个微服务

[](https://medium.com/@wenqiglantz?source=post_page---byline--cc065d0210ef--------------------------------)[![Wenqi Glantz](../Images/65b518863e01aaa48ecc6b8ac6d1be60.png)](https://medium.com/@wenqiglantz?source=post_page---byline--cc065d0210ef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cc065d0210ef--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cc065d0210ef--------------------------------) [Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--cc065d0210ef--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cc065d0210ef--------------------------------) ·阅读时间：11分钟·2024年2月21日

--

![](../Images/f810b68ec0b025724d0f45befe4b6cdb.png)

图像由作者通过DALL-E 3生成

在探索企业级RAG的过程中，本文介绍了如何从在Colab笔记本中开发的RAG管道POC中创建RAG微服务。我们采取了以下方法：

+   使用LlamaIndex的`create-llama`命令行工具生成RAG微服务的模板代码。

+   开发两个微服务：`ingestion-service`和`inference-service`，覆盖RAG的两个主要阶段。

+   将Colab笔记本中的代码逻辑转换为微服务。

+   将Milvus向量数据库集成到我们的新微服务中。

+   将NeMo Guardrails添加到`inference-service`，为用户输入、LLM输出、主题审核和自定义操作提供保护机制，并与LlamaIndex集成。

# 笔记本

为了快速原型开发，Colab笔记本因其易用性、可访问性和免费使用而成为理想选择。

例如，[这个Colab笔记本](https://colab.research.google.com/drive/1EvyAIflOtyAwNbtghNv0i2asSHuPV-VN?usp=sharing)演示了如何在RAG管道中使用元数据替换+节点句子窗口，该管道用作[NVIDIA AI企业用户指南](https://docs.nvidia.com/ai-enterprise/latest/pdf/nvidia-ai-enterprise-user-guide.pdf)的聊天机器人。
