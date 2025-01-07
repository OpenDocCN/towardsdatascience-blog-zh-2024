# 使用 Apple 硬件运行 LLM 推理

> 原文：[https://towardsdatascience.com/run-llm-inference-using-apple-hardware-00a4a5d455b7?source=collection_archive---------8-----------------------#2024-01-30](https://towardsdatascience.com/run-llm-inference-using-apple-hardware-00a4a5d455b7?source=collection_archive---------8-----------------------#2024-01-30)

## 解锁 Apple GPU 的强大功能以进行 LLM 推理，配合 MLX 使用

[](https://medium.com/@christopher_karg?source=post_page---byline--00a4a5d455b7--------------------------------)[![Christopher Karg](../Images/9d163d59e0c3167732f55d497caf9db2.png)](https://medium.com/@christopher_karg?source=post_page---byline--00a4a5d455b7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--00a4a5d455b7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--00a4a5d455b7--------------------------------) [Christopher Karg](https://medium.com/@christopher_karg?source=post_page---byline--00a4a5d455b7--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--00a4a5d455b7--------------------------------) ·阅读时间 15 分钟·2024 年 1 月 30 日

--

![](../Images/43b051fdf06c8fc88b6ddbaf1bc274cb.png)

来源：[https://www.pexels.com/photo/train-railway-near-trees-552779/](https://www.pexels.com/photo/train-railway-near-trees-552779/)

# 最后！

我们现在可以使用 Apple 的原生硬件进行推理并微调我们自己的 LLM（大语言模型）。本文将介绍如何创建自己的实验并运行推理。在未来，我将发布一篇关于如何微调这些 LLM 的文章（同样使用 Apple 硬件）。

如果你还没有阅读我之前的文章，我建议你去看看，因为我阐述了为什么你应该考虑托管（并且 [微调](https://medium.com/towards-data-science/how-to-efficiently-fine-tune-your-own-open-source-llm-using-novel-techniques-code-provided-03a4e67d1b48)）你自己的开源 LLM。我还讨论了如何 [优化这个过程](https://medium.com/towards-data-science/quantisation-and-co-reducing-inference-times-on-llms-by-80-671db9349bdb)，以减少推理和训练时间。我将简要提及一些主题，比如量化（quantisation），因为这些内容在前面提到的文章中已有深入讨论。

我将使用 [mlx](https://github.com/ml-explore) 框架与 [Meta 的 Llama2 模型](https://ai.meta.com/llama/) 结合使用。有关如何访问这些模型的详细信息，可以参考我的 [上一篇文章](https://medium.com/towards-data-science/quantisation-and-co-reducing-inference-times-on-llms-by-80-671db9349bdb)。不过，我也会在本文中简要解释如何进行操作。

让我们开始吧。

# 先决条件：

1.  配备 M 系列芯片的机器（M1/M2/M3）

1.  操作系统 >= 13.0

1.  Python 版本在 3.8–3.11 之间
