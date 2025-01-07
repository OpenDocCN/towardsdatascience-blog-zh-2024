# Torch 编译：以低努力实现 2 倍更快的 Llama 3.2

> 原文：[https://towardsdatascience.com/torch-compile-2x-faster-llama-3-2-with-low-effort-d17c102ac405?source=collection_archive---------2-----------------------#2024-11-13](https://towardsdatascience.com/torch-compile-2x-faster-llama-3-2-with-low-effort-d17c102ac405?source=collection_archive---------2-----------------------#2024-11-13)

## 但这将取决于你的 GPU。

[](https://medium.com/@bnjmn_marie?source=post_page---byline--d17c102ac405--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--d17c102ac405--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d17c102ac405--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d17c102ac405--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--d17c102ac405--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d17c102ac405--------------------------------) ·阅读时间 5 分钟·2024年11月13日

--

![](../Images/8149a5a73c8bea9de7489e3fb32fb911.png)

图像由 ChatGPT 生成。

Torch 编译（`torch.compile`）首次在 PyTorch 2.0 中引入，但在经历了几次更新和优化之后，才可以稳定地支持大多数大型语言模型（LLM）。

在推理时，`torch.compile` 可以通过仅小幅增加内存使用量来显著加速解码过程。

在本文中，我们将探讨 `torch.compile` 的工作原理，并衡量它对 LLM 推理性能的影响。要在代码中使用 `torch.compile`，你只需添加一行代码。对于本文，我用 Llama 3.2 进行了测试，并尝试了 `bitsandbytes` 量化，使用了两种不同的 GPU：Google Colab 的 L4 和 A100。

我还创建了一个笔记本，演示如何使用`torch.compile`并对其性能进行基准测试，点击这里查看：

[获取笔记本 (#120)](https://newsletter.kaitchup.com/p/notebooks)

# Torch 编译：它如何让模型更快？

`torch.compile` 提供了一种通过将标准 PyTorch 代码转换为优化过的机器码来加速模型的方法。这种方法称为 JIT（即时）编译，使代码能够在特定硬件上更高效地运行，即比普通 Python 代码更快。它对于复杂模型尤其有效，即便是微小的速度提升也能产生显著效果……
