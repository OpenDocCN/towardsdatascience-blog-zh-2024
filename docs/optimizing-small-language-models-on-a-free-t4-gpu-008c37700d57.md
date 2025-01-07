# 在免费的 T4 GPU 上优化小型语言模型

> 原文：[`towardsdatascience.com/optimizing-small-language-models-on-a-free-t4-gpu-008c37700d57?source=collection_archive---------7-----------------------#2024-01-30`](https://towardsdatascience.com/optimizing-small-language-models-on-a-free-t4-gpu-008c37700d57?source=collection_archive---------7-----------------------#2024-01-30)

## 使用直接偏好优化（DPO）微调 Phi-2 的全面指南

[](https://medium.com/@yanli.liu?source=post_page---byline--008c37700d57--------------------------------)![Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--008c37700d57--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--008c37700d57--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--008c37700d57--------------------------------) [Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--008c37700d57--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--008c37700d57--------------------------------) ·阅读时长 11 分钟·2024 年 1 月 30 日

--

![](img/658323d7ef569529110ababc07d7f772.png)

图片来自[Donald Wu](https://unsplash.com/@donaldwuid?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

“小型”语言模型（LLMs）正在迅速成为人工智能领域的一项变革性技术。

与传统的语言模型（LLMs）需要大量计算资源不同，这些模型要小得多且更高效。尽管它们的性能可以与较大的模型相媲美，但它们能够轻松在标准设备（如笔记本电脑）上运行，甚至可以部署到边缘设备。这也意味着它们可以轻松定制并集成到你的数据集上使用。

在本文中，我将首先解释模型微调和对齐过程的基础知识和内部工作原理。然后，我将引导你通过使用一种名为直接偏好优化（Direct Preference Optimization，DPO）的创新方法对 2 亿参数的小型语言模型 Phi 2 进行偏好微调的过程。

由于模型体积小且采用了量化和 QLoRA 等优化技术，我们将能够通过使用免费的 T4 GPU 在 Google Colab 中执行此过程！这需要一些调整 Hugging Face 用于训练其 Zephyr 7B 模型的设置和超参数。

## 目录：

1.  **为什么我们需要微调**…
