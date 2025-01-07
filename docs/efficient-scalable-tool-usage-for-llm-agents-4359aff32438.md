# 高效且可扩展的工具使用——LLM 代理

> 原文：[https://towardsdatascience.com/efficient-scalable-tool-usage-for-llm-agents-4359aff32438?source=collection_archive---------4-----------------------#2024-06-06](https://towardsdatascience.com/efficient-scalable-tool-usage-for-llm-agents-4359aff32438?source=collection_archive---------4-----------------------#2024-06-06)

## 利用干净的抽象层次，避免工具膨胀提示大小，并提升代理的性能

[](https://medium.com/@janekmajewski?source=post_page---byline--4359aff32438--------------------------------)[![Jan Majewski](../Images/2d06418ffe9f14cb558ebaec7f871cf0.png)](https://medium.com/@janekmajewski?source=post_page---byline--4359aff32438--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4359aff32438--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4359aff32438--------------------------------) [Jan Majewski](https://medium.com/@janekmajewski?source=post_page---byline--4359aff32438--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4359aff32438--------------------------------) ·阅读时间9分钟·2024年6月6日

--

![](../Images/fb43bbebee338aa4be5201cff5434221.png)

图片来自[马丁·桑切斯](https://unsplash.com/@martinsanchez?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# LLM 代理是一个强大的框架，将 LLM 的性能提升到一个新的水平…

代理框架利用大型语言模型作为决策引擎，通过多阶段推理解决复杂任务。这种方法产生了令人印象深刻的结果，因为它将 LLM 的内部知识与通过用例定制工具进行行动的能力相结合。工具还使模型能够获取最新的领域特定知识，并与我们系统的其他部分进行交互。

通过创建多个特定角色的代理，框架可以进一步提升，能够通过协同工作实现最佳效果。例如，你可以将程序员、测试员、执行器和调试器组合在一起，比单一复杂的代理更高效地编写工作代码。

# …然而，代理是“吃 token”的，这可能会推高成本和延迟

尽管代理框架具有令人印象深刻的能力，但它也有其缺点。我们调用代理时，大多数情况下需要预期至少双倍的 token 使用量和延迟，相比于…
