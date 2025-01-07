# RAGOps 指南：构建和扩展检索增强生成系统

> 原文：[https://towardsdatascience.com/ragops-guide-building-and-scaling-retrieval-augmented-generation-systems-3d26b3ebd627?source=collection_archive---------0-----------------------#2024-11-26](https://towardsdatascience.com/ragops-guide-building-and-scaling-retrieval-augmented-generation-systems-3d26b3ebd627?source=collection_archive---------0-----------------------#2024-11-26)

## 学习检索增强生成

## 有效RAG实施的架构、操作层和最佳实践

[](https://medium.com/@abhinavkimothi?source=post_page---byline--3d26b3ebd627--------------------------------)[![Abhinav Kimothi](../Images/ed5548c99e1910e7dc35bfcac26cb314.png)](https://medium.com/@abhinavkimothi?source=post_page---byline--3d26b3ebd627--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3d26b3ebd627--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3d26b3ebd627--------------------------------) [Abhinav Kimothi](https://medium.com/@abhinavkimothi?source=post_page---byline--3d26b3ebd627--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3d26b3ebd627--------------------------------) ·阅读时间 22 分钟·2024 年 11 月 26 日

--

![](../Images/a13dfca856a3787567c94690da52a668.png)

RAG 运营（来源：作者使用[Dall-E 3](https://openai.com/index/dall-e-3/)生成的图像）

检索增强生成（RAG）毫不奇怪地成为生成式人工智能和大型语言模型应用领域中最常用的技术之一。事实上，根据一份[Databricks报告](https://intelliarts.com/blog/retrieval-augmented-generation-language-models/#:~:text=As%20evidence%2C%20Databricks%20reports%20that,RAG%20seems%20more%20than%20beneficial.)，超过60%的LLM应用以某种形式使用RAG。因此，在当前[价值约60亿美元且年增长率接近40%](https://www.grandviewresearch.com/industry-analysis/large-language-model-llm-market-report)的全球LLM市场中，RAG 无疑成为必须掌握的关键技术之一。

今天构建 PoC RAG pipeline 并不太具有挑战性。有很多现成的代码示例，利用像[LangChain](https://github.com/abhinav-kimothi/A-Simple-Guide-to-RAG)或[LlamaIndex](https://docs.llamaindex.ai/en/stable/optimizing/production_rag/)这样的框架，以及无代码/低代码平台，如[RAGArch](https://www.llamaindex.ai/blog/ragarch-building-a-no-code-rag-pipeline-configuration-one-click-rag-code-generation-tool-powered-b6e8eeb70089?utm_source=chatgpt.com)，[HelloRAG](https://hellorag.ai/)等。

[](https://medium.com/@abhinavkimothi/conversing-with-documents-unleashing-the-power-of-llms-and-langchain-397838127fd?source=post_page-----3d26b3ebd627--------------------------------) [## 与文档对话：释放大型语言模型和LangChain的强大潜力

### 使用LangChain构建一个Streamlit应用程序，用于与文档和链接进行问答。

medium.com](https://medium.com/@abhinavkimothi/conversing-with-documents-unleashing-the-power-of-llms-and-langchain-397838127fd?source=post_page-----3d26b3ebd627--------------------------------)

另一方面，一个生产级的RAG系统由多个专业化的层组成…
