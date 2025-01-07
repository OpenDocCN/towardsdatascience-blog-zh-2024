# 你的服务器在自托管LLM时能承受多少压力？

> 原文：[https://towardsdatascience.com/load-testing-self-hosted-llms-29ca8a4cf43a?source=collection_archive---------4-----------------------#2024-10-19](https://towardsdatascience.com/load-testing-self-hosted-llms-29ca8a4cf43a?source=collection_archive---------4-----------------------#2024-10-19)

## 你需要更多的GPU还是一块现代的GPU？你如何做出基础设施的决策？

[](https://thuwarakesh.medium.com/?source=post_page---byline--29ca8a4cf43a--------------------------------)[![Thuwarakesh Murallie](../Images/44f1a14a899426592bbd8c7f73ce169d.png)](https://thuwarakesh.medium.com/?source=post_page---byline--29ca8a4cf43a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--29ca8a4cf43a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--29ca8a4cf43a--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--29ca8a4cf43a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--29ca8a4cf43a--------------------------------) ·6分钟阅读·2024年10月19日

--

![](../Images/840591a9641d0029ef5c5cca767dffe3.png)

图像由作者使用Dalle-E-2024创建

当一群用户突然开始使用一个之前只有你和你的开发团队使用过的应用时，是什么感觉？

这就是从原型到生产阶段的百万美元问题。

就LLM而言，你可以进行几十次调整，以在预算和可接受的质量范围内运行你的应用。例如，你可以选择一个量化模型来降低内存使用。或者你可以微调一个小型模型，超越巨型LLM的性能。

[](/i-fine-tuned-the-tiny-llama-3-2-1b-to-replace-gpt-4o-7ce1e5619f3d?source=post_page-----29ca8a4cf43a--------------------------------) [## 我对Tiny Llama 3.2 1B进行了微调，以替代GPT-4o

### 微调的努力是否比少量示例提示更值得？

towardsdatascience.com](/i-fine-tuned-the-tiny-llama-3-2-1b-to-replace-gpt-4o-7ce1e5619f3d?source=post_page-----29ca8a4cf43a--------------------------------)

你甚至可以调整你的基础设施，以获得更好的结果。例如，你可能想要将使用的GPU数量翻倍，或者选择最新一代的GPU。

但你如何证明选项A比选项B和C表现更好呢？

这是我们在进入生产阶段时，最早期需要问自己的一个重要问题。所有这些选项都有它们的成本——…
