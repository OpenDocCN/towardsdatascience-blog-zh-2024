# NeMo Guardrails：终极开源LLM安全工具包

> 原文：[https://towardsdatascience.com/nemo-guardrails-the-ultimate-open-source-llm-security-toolkit-0a34648713ef?source=collection_archive---------3-----------------------#2024-02-09](https://towardsdatascience.com/nemo-guardrails-the-ultimate-open-source-llm-security-toolkit-0a34648713ef?source=collection_archive---------3-----------------------#2024-02-09)

## 探索NeMo Guardrails的实际应用案例

[](https://medium.com/@wenqiglantz?source=post_page---byline--0a34648713ef--------------------------------)[![Wenqi Glantz](../Images/65b518863e01aaa48ecc6b8ac6d1be60.png)](https://medium.com/@wenqiglantz?source=post_page---byline--0a34648713ef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0a34648713ef--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0a34648713ef--------------------------------) [Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--0a34648713ef--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0a34648713ef--------------------------------) ·阅读时间13分钟·2024年2月9日

--

![](../Images/4b024c90d7fa9e4a18ffaf35c22f7658.png)

图片由DALL-E 3生成，由作者提供

在LLM安全性话题中，我们已经从不同角度探讨了[OWASP top 10 for LLM applications](https://levelup.gitconnected.com/security-driven-development-with-owasp-top-10-for-llm-applications-588406f40d4c?sk=dde699f26d74e8bcfb1ea2c4488b62e5)、[Llama Guard](/safeguarding-your-rag-pipelines-a-step-by-step-guide-to-implementing-llama-guard-with-llamaindex-6f80a2e07756?sk=c6cc48013bac60924548dd4e1363fa9e)和[Lighthouz AI](/jump-start-your-rag-pipelines-with-advanced-retrieval-llamapacks-and-benchmark-with-lighthouz-ai-80a09b7c7d9d?sk=14e50a68f9ef825aaa6634365c7d9617)。今天，我们将探索[NVIDIA开发的开源工具包NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails)，该工具包可轻松为基于LLM的对话系统添加可编程防护措施。

# NeMo Guardrails vs. Llama Guard

NeMo Guardrails与我们在[上一篇文章](https://medium.com/towards-data-science/safeguarding-your-rag-pipelines-a-step-by-step-guide-to-implementing-llama-guard-with-llamaindex-6f80a2e07756?sk=c6cc48013bac60924548dd4e1363fa9e)中深入探讨过的Llama Guard有何不同？让我们并排比较它们的功能。

![](../Images/e4f572800f573e751fb60ed1f1006bca.png)

表格由作者提供

正如我们所看到的，Llama Guard和NeMo Guardrails在本质上是不同的：

+   Llama Guard 是一个大型语言模型，基于 Llama 2 进行微调，并且是一个输入输出安全防护模型。它包含六个不安全类别，开发者可以通过添加额外的不安全类别来定制这些类别，以适应他们的输入输出审查需求。

+   NeMo Guardrails 是一个更为全面的 LLM 安全工具集，提供了更广泛的可编程功能…
