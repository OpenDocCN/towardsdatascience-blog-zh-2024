# 如何从文档中创建RAG评估数据集

> 原文：[https://towardsdatascience.com/how-to-create-a-rag-evaluation-dataset-from-documents-140daa3cbe71?source=collection_archive---------0-----------------------#2024-11-03](https://towardsdatascience.com/how-to-create-a-rag-evaluation-dataset-from-documents-140daa3cbe71?source=collection_archive---------0-----------------------#2024-11-03)

## 使用LLM自动创建任何语言的领域特定数据集

[](https://medium.com/@leoneversberg?source=post_page---byline--140daa3cbe71--------------------------------)[![Dr. Leon Eversberg](../Images/56dc3579a29933f7047a9ce60be4697a.png)](https://medium.com/@leoneversberg?source=post_page---byline--140daa3cbe71--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--140daa3cbe71--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--140daa3cbe71--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--140daa3cbe71--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--140daa3cbe71--------------------------------) ·12分钟阅读·2024年11月3日

--

![](../Images/25266f63b13f62850a4bae615c031615.png)

我们在Hugging Face Hub上自动生成的RAG评估数据集（[PDF输入文件](https://eur-lex.europa.eu/legal-content/EN/TXT/PDF/?uri=OJ%3AL_202401689&qid=1730538361265)来自欧盟，遵循[CC BY 4.0](https://european-union.europa.eu/legal-notice_en)许可）。图片由作者提供

在本文中，我将向你展示如何从任何语言的文档中创建你自己的RAG数据集，包括上下文、问题和答案。

检索增强生成（RAG）[1]是一种技术，允许LLM访问外部知识库。

通过上传PDF文件并将其存储在向量数据库中，我们可以通过向量相似度搜索来检索这些知识，然后将检索到的文本插入到LLM的提示中作为附加上下文。

这为LLM提供了新的知识，并减少了LLM编造事实（幻觉）的可能性。

![](../Images/3f9f8d02b5517dcf7fd7ce5a580e8a67.png)

基本的RAG管道。图片由作者提供，来自文章[“如何使用RAG构建本地开源LLM聊天机器人”](/how-to-build-a-local-open-source-llm-chatbot-with-rag-f01f73e2a131)

然而，在RAG管道中有许多参数需要设置，研究人员总是在提出新的改进建议。我们如何知道选择哪些参数，哪些方法能真正提高我们特定用例的性能呢？

这就是为什么我们需要一个验证/开发/测试数据集来评估我们的RAG管道。数据集应来自我们感兴趣的领域……
