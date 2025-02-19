# 在较低显存 GPU 上运行 PixArt-Σ/Flux.1 图像生成：Python 简短教程

> 原文：[`towardsdatascience.com/running-pixart-%CF%83-flux-1-image-generation-on-lower-vram-gpus-a-short-tutorial-in-python-62419f35596e?source=collection_archive---------6-----------------------#2024-08-27`](https://towardsdatascience.com/running-pixart-%CF%83-flux-1-image-generation-on-lower-vram-gpus-a-short-tutorial-in-python-62419f35596e?source=collection_archive---------6-----------------------#2024-08-27)

## Diffusers 和 Quanto 为 GPU 性能不足的用户带来了希望

[](https://medium.com/@CVxTz?source=post_page---byline--62419f35596e--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--62419f35596e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--62419f35596e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--62419f35596e--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--62419f35596e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--62419f35596e--------------------------------) ·5 分钟阅读·2024 年 8 月 27 日

--

![](img/fbcf6506ac6b48db849c0b5cd5760a3d.png)

由 PixArt-Σ在本地生成，使用的显存小于 8GB

图像生成工具比以往任何时候都更加热门，且它们的功能更加强大。像 PixArt Sigma 和 Flux.1 这样的模型处于领先地位，得益于它们开放的模型权重和宽松的许可协议。这个设置允许进行创意性的调整，包括在不共享数据的情况下训练 LoRA。

然而，如果你使用的是较旧或显存较少的 GPU，使用这些模型可能会遇到挑战。通常在质量、速度和显存使用之间需要做出权衡。在这篇博文中，我们将重点讨论如何优化速度和降低显存使用，同时尽可能保持质量。这种方法在 PixArt 中表现得尤为出色，因为它的模型较小，但在 Flux.1 中效果可能有所不同。我将在文末分享一些针对 Flux.1 的替代解决方案。

PixArt Sigma 和 Flux.1 都是基于变换器的，这意味着它们能够受益于与大型语言模型（LLM）相同的量化技术。量化涉及压缩模型的各个组成部分，以减少内存使用。这使得你可以同时将所有模型组件存储在 GPU 显存中，从而与将权重在不同存储器之间移动的方法相比，生成速度更快。
