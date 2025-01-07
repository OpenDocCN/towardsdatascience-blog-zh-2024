# 使用 Amazon Bedrock 和 LangChain 构建 QA 研究聊天机器人

> 原文：[https://towardsdatascience.com/building-a-qa-research-chatbot-with-amazon-bedrock-and-langchain-677fbd19e3c1?source=collection_archive---------4-----------------------#2024-03-16](https://towardsdatascience.com/building-a-qa-research-chatbot-with-amazon-bedrock-and-langchain-677fbd19e3c1?source=collection_archive---------4-----------------------#2024-03-16)

## 使用 Python 的概述和实现

[](https://medium.com/@aashishnair?source=post_page---byline--677fbd19e3c1--------------------------------)[![Aashish Nair](../Images/23f4b3839e464419332b690a4098d824.png)](https://medium.com/@aashishnair?source=post_page---byline--677fbd19e3c1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--677fbd19e3c1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--677fbd19e3c1--------------------------------) [Aashish Nair](https://medium.com/@aashishnair?source=post_page---byline--677fbd19e3c1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--677fbd19e3c1--------------------------------) ·9 分钟阅读·2024年3月16日

--

![](../Images/7929fce02d0fff42fbc3101127223951.png)

图片由[Chen](https://pixabay.com/users/chenspec-7784448/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5474990)提供，来源：[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5474990)

## 目录

∘ [介绍](#d86a)

∘ [目标](#3a80)

∘ [聊天机器人架构](#64d9)

∘ [技术栈](#afbf)

∘ [过程](#cea6)

∘ [步骤 1 — 加载 PDF 文档](#0b09)

∘ [步骤 2 — 构建向量存储](#c4c2)

∘ [步骤 3 — 加载 LLM](#273b)

∘ [步骤 4 — 创建检索链](#2182)

∘ [步骤 5 — 构建用户界面](#19b3)

∘ [步骤 6 — 运行聊天机器人应用程序](#1867)

∘ [步骤 7 — 容器化应用程序](#bc76)

∘ [未来步骤](#9129)

∘ [结论](#7f58)

∘ [参考文献](#4f67)

## 介绍

不久前，我尝试了[构建一个完全在我的 CPU 上运行的简单自定义聊天机器人](https://medium.com/towards-data-science/can-a-llama-2-powered-chatbot-be-trained-on-a-cpu-ce9ec6ebe3c2)。

结果令人震惊，应用程序频繁崩溃。尽管如此，这并不令人惊讶。事实证明，将一个13B参数的模型放在一台600美元的电脑上，相当于让一个蹒跚学步的孩子爬山。
