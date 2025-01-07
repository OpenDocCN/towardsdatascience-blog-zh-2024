# 使用Python构建GenAI：从零开始构建代理（完整教程）

> 原文：[https://towardsdatascience.com/genai-with-python-build-agents-from-scratch-complete-tutorial-4fc1e084e2ec?source=collection_archive---------0-----------------------#2024-09-29](https://towardsdatascience.com/genai-with-python-build-agents-from-scratch-complete-tutorial-4fc1e084e2ec?source=collection_archive---------0-----------------------#2024-09-29)

## 使用Ollama、LangChain、LangGraph（无GPU，无APIKEY）

[](https://maurodp.medium.com/?source=post_page---byline--4fc1e084e2ec--------------------------------)[![Mauro Di Pietro](../Images/3586d9d3238d904a1e1fa39c77b59d3f.png)](https://maurodp.medium.com/?source=post_page---byline--4fc1e084e2ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4fc1e084e2ec--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4fc1e084e2ec--------------------------------) [Mauro Di Pietro](https://maurodp.medium.com/?source=post_page---byline--4fc1e084e2ec--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4fc1e084e2ec--------------------------------) ·15分钟阅读·2024年9月29日

--

![](../Images/a4b4f821b7e226c94d46a7574097b60f.png)

图片由[Mathieu Stern](https://unsplash.com/@mathieustern?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*(除非另有说明，否则所有图片均为作者提供)*

## 简介

**提示工程**是设计和优化提示（文本输入）以增强大语言模型（LLMs）行为的实践。目标是通过精心设计指令，获得模型所需的回应。最常用的提示技术包括：

+   **连锁思维：**涉及生成逐步推理过程以得出结论。通过明确列出导致最终答案的逻辑步骤，推动模型“边想边说”。

+   **ReAct** **（推理+行动）：**将推理与行动相结合。模型不仅会思考问题，还会根据其推理采取行动。因此，它更具互动性，模型在推理步骤和行动之间交替进行，逐步完善其方法。基本上，这是一个“思考”，“行动”，“观察”的循环。

> 举个例子：想象一下要求一个 AI “找到一台价格低于 $1000 的最佳笔记本电脑”。
> 
> - **普通回答**：“联想 Thinkpad”。
> 
> **- 连锁思维回答**：“我需要考虑性能、电池寿命等因素……”
